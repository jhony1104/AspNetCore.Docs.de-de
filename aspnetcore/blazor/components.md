---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/components
ms.openlocfilehash: e51f6745f6e0c748e51d7f8a49193f3d81fd2a06
ms.sourcegitcommit: 07cd66e367d080acb201c7296809541599c947d1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71039186"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Erstellen und Verwenden von ASP.net Core Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Blazor-apps werden mithilfe von *Komponenten*erstellt. Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular. Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist. Komponenten sind flexibel und leicht zu gestalten. Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.

## <a name="component-classes"></a>Komponenten Klassen

Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert. Eine Komponente in blazor wird formal als *Razor-Komponente*bezeichnet.

Der Name einer Komponente muss mit einem Großbuchstaben beginnen. Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.

Komponenten können mit der Dateierweiterung *. cshtml* erstellt werden, sofern die Dateien mithilfe der `_RazorComponentInclude` MSBuild-Eigenschaft als Razor-Komponenten Dateien identifiziert werden. Beispielsweise eine APP, die angibt, dass alle *cshtml* -Dateien unter dem Ordner *pages* als Razor-Komponenten Dateien behandelt werden sollen:

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

Die Benutzeroberfläche für eine Komponente wird mit HTML definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt. Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert. Der Name der generierten Klasse entspricht dem Namen der Datei.

Member der Komponentenklasse werden in einem `@code`-Block definiert. Im- `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben. Mehrere `@code`-Blöcke sind zulässig.

> [!NOTE]
> In früheren Vorschauen von ASP.net Core 3,0 `@functions` wurden Blöcke für denselben Zweck verwendet wie `@code` Blöcke in Razor-Komponenten. `@functions`-Blöcke funktionieren weiterhin in Razor-Komponenten, aber es wird empfohlen `@code` , den-Block in ASP.net Core 3,0 Preview 6 oder höher zu verwenden.

Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden `@`, die mit beginnen. Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird. Im folgenden Beispiel wird ausgewertet und gerendert:

* `_headingFontStyle`zum CSS-Eigenschafts Wert `font-style`für.
* `_headingText`zum Inhalt des `<h1>` -Elements.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut. Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen am Dokumentobjektmodell des Browsers (DOM) an.

Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden. Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* . Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird. Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports. Razor* -Datei der APP hinzu. Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace `WebApplication`der APP lautet:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrieren von Komponenten in Razor Pages und MVC-apps

Verwenden Sie Komponenten mit vorhandenen Razor Pages und MVC-apps. Es ist nicht erforderlich, vorhandene Seiten oder Sichten für die Verwendung von Razor-Komponenten neu zu schreiben. Wenn die Seite oder die Ansicht gerendert wird, werden die Komponenten gleichzeitig vorab in eine Vorabversion übernommen.

Verwenden Sie die `RenderComponentAsync<TComponent>` HTML-Hilfsmethode, um eine Komponente von einer Seite oder Sicht zu Rendering:

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall. Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte. Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.

Weitere Informationen zum Rendern von Komponenten und zum Verwalten des <xref:blazor/hosting-models> Komponenten Status in blazor-Server-apps finden Sie im Artikel.

## <a name="use-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Bei der Attribut Bindung wird Groß-/Kleinschreibung Beispielsweise `@bind` ist gültig, und `@Bind` ist ungültig.

Das folgende Markup in *Index. Razor* rendert `HeadingComponent` eine-Instanz:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Komponenten/headingcomponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist. Wenn Sie `@using` eine-Anweisung für den-Namespace der Komponente hinzufügen, wird die-Komponente verfügbar, wodurch die Warnung entfernt wird.

## <a name="component-parameters"></a>Komponentenparameter

Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]` -Attribut definiert werden. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

*Komponenten/childcomponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

Im folgenden Beispiel `ParentComponent` legt den Wert `Title` der-Eigenschaft von `ChildComponent`fest.

*Pages/para Component. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Untergeordneter Inhalt

Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden. Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.

Im folgenden Beispiel `ChildComponent` verfügt über eine `ChildContent` -Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt. Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll. Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des Bootstrap-Panels `panel-body`gerendert.

*Komponenten/childcomponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Die Eigenschaft, die `RenderFragment` den Inhalt empfängt, `ChildContent` muss gemäß der Konvention benannt werden.

Im folgenden `ParentComponent` können Inhalte zum `ChildComponent` Rendern von bereitgestellt werden, indem der `<ChildComponent>` Inhalt innerhalb der Tags platziert wird.

*Pages/para Component. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribut-Verteilung und beliebige Parameter

Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering. Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe [@attributes](xref:mvc/views/razor#attributes) der Razor-Direktive gerendert wird. Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt. Beispielsweise kann es mühsam sein, Attribute separat für einen `<input>` zu definieren, der viele Parameter unterstützt.

Im folgenden `<input>` Beispiel verwendet das erste-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>` -Element (`id="useAttributesDict"`) Attribut-splatting verwendet:

```cshtml
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

Der Typ des Parameters muss mit `IEnumerable<KeyValuePair<string, object>>` Zeichen folgen Schlüsseln implementiert werden. Die `IReadOnlyDictionary<string, object>` Verwendung von ist in diesem Szenario auch eine Option.

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

Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter `[Parameter]` mit dem- `CaptureUnmatchedValues` Attribut, wobei `true`die-Eigenschaft auf festgelegt ist:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Die `CaptureUnmatchedValues` -Eigenschaft `[Parameter]` in ermöglicht dem-Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen. Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren. Der Eigenschaftentyp `CaptureUnmatchedValues` , der mit verwendet wird `Dictionary<string, object>` , muss mit Zeichen folgen Schlüsseln zugewiesen werden können. `IEnumerable<KeyValuePair<string, object>>`oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.

## <a name="data-binding"></a>Datenbindung

Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit [@bind](xref:mvc/views/razor#bind) dem-Attribut. Im folgenden Beispiel wird das `_italicsCheck` Feld an den aktivierten Zustand des Kontrollkästchens gebunden:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

Wenn das Kontrollkästchen aktiviert und deaktiviert ist, wird der Wert der Eigenschaft auf `true` `false`bzw. aktualisiert.

Das Kontrollkästchen wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts. Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen normalerweise sofort in der Benutzeroberfläche

Die `@bind` Verwendung von `CurrentValue` mit einer`<input @bind="CurrentValue" />`-Eigenschaft () entspricht im wesentlichen folgendem:

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

Wenn die Komponente gerendert wird `value` , stammt der des Eingabe Elements aus `CurrentValue` der-Eigenschaft. Wenn der Benutzer in das Textfeld eingibt, `onchange` wird das-Ereignis ausgelöst `CurrentValue` , und die-Eigenschaft wird auf den geänderten Wert festgelegt. Tatsächlich ist die Codegenerierung etwas komplexer, da `@bind` einige Fälle behandelt werden, in denen Typkonvertierungen ausgeführt werden. Im Prinzip `@bind` ordnet den aktuellen Wert eines Ausdrucks einem `value` -Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.

Zusätzlich `onchange` zur Behandlung von Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [@bind-value](xref:mvc/views/razor#bind) Attribut mit einem `event` -Parameter[@bind-value:event](xref:mvc/views/razor#bind)() angegeben wird. Im folgenden Beispiel wird die `CurrentValue` -Eigenschaft für `oninput` das-Ereignis gebunden:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

Anders `onchange`als, das ausgelöst wird, wenn das Element `oninput` den Fokus verliert, wird ausgelöst, wenn der Wert des Textfelds geändert wird.

**Nicht zu erteilbare Werte**

Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.

Betrachten Sie das folgende Szenario:

* Ein `<input>` Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Der Benutzer aktualisiert den Wert des-Elements auf `123.45` der Seite auf und ändert den Element Fokus.

Im vorangehenden Szenario wird der Wert des-Elements auf zurück `123`gesetzt. Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.

Standardmäßig gilt die-Bindung für das- `onchange` Ereignis des`@bind="{PROPERTY OR FIELD}"`-Elements (). Verwenden `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` Sie, um ein anderes Ereignis festzulegen. Für das `oninput` -Ereignis`@bind-value:event="oninput"`() tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt. Wenn das `oninput` Ereignis mit einem `int`-gebundenen Typ als Ziel verwendet wird, wird verhindert, dass `.` ein Benutzer ein Zeichen eingibt. Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind. Es gibt Szenarien, in denen das Wiederherstellen des `oninput` Werts für das Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht `<input>` zu testbaren Wert löschen darf. Zu Alternativen gehören:

* Verwenden Sie das `oninput` Ereignis nicht. Verwenden Sie das `onchange` Standard Ereignis`@bind="{PROPERTY OR FIELD}"`(), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.
* Binden Sie an einen Typ, der NULL- `int?` Werte `string`zulässt, wie z. b. oder, und stellen Sie eine benutzerdefinierte Logik bereit,
* Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z `InputNumber` . `InputDate`b. oder. Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben. Komponenten Validierungs Komponenten:
  * Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler für `EditContext`die zugeordnete zu empfangen.
  * Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.

**Globalisierung**

`@bind`Werte werden für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert.

Der Zugriff auf die aktuelle Kultur ist über <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> die-Eigenschaft möglich.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:

* `date`
* `number`

Die vorangehenden Feldtypen:

* Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.
* Darf keinen frei Form Text enthalten.
* Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.

Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:

* `datetime-local`
* `month`
* `week`

`@bind`unterstützt `@bind:culture` den-Parameter, <xref:System.Globalization.CultureInfo?displayProperty=fullName> um einen zum Auswerten und Formatieren eines-Werts bereitzustellen. Die Angabe einer Kultur ist nicht empfehlenswert, `date` Wenn `number` die Feldtypen und verwendet werden. `date`und `number` verfügen über eine integrierte Unterstützung für blazor, die die erforderliche Kultur bereitstellt.

Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .

**Format Zeichenfolgen**

Die Datenbindung funktioniert <xref:System.DateTime> mit Format Zeichenfolgen mithilfe [@bind:format](xref:mvc/views/razor#bind)von. Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Im vorangehenden Code wird für `<input>` `text`den Feldtyp des Elements`type`() der Standardwert verwendet. `@bind:format`wird für die Bindung der folgenden .NET-Typen unterstützt:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Das `@bind:format` -Attribut gibt das Datumsformat an, das `value` auf den `<input>` des Elements angewendet werden soll. Das-Format wird auch verwendet, um den Wert zu analysieren `onchange` , wenn ein Ereignis auftritt.

Es wird nicht empfohlen, `date` ein Format für den Feldtyp anzugeben, da blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt.

**Komponenten Parameter**

Die Bindung erkennt Komponenten Parameter, `@bind-{property}` wobei einen Eigenschafts Wert über Komponenten hinweg binden kann.

Die folgende untergeordnete Komponente`ChildComponent`() verfügt `Year` über einen Komponenten `YearChanged` Parameter und einen Rückruf:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`wird im Abschnitt " [EventCallback](#eventcallback) " erläutert.

Die folgende übergeordnete Komponente `ChildComponent` verwendet und bindet `ParentYear` den-Parameter vom übergeordneten `Year` an den-Parameter in der untergeordneten Komponente:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Das Laden von erzeugt das folgende Markup: `ParentComponent`

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear` Wenn der Wert der Eigenschaft durch Auswählen der Schaltfläche `ParentComponent`im geändert wird `ChildComponent` , wird die `Year` -Eigenschaft von aktualisiert. Der neue Wert von `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Der `Year` -Parameter ist bindbar, da er über `YearChanged` ein Begleit Ereignis verfügt, das mit `Year` dem Typ des Parameters übereinstimmt.

Gemäß der Konvention `<ChildComponent @bind-Year="ParentYear" />` entspricht im Wesentlichen dem Schreiben:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` des-Attributs an einen entsprechenden Ereignishandler gebunden werden. Beispielsweise kann die- `MyProp` Eigenschaft mithilfe der folgenden `MyEventHandler` zwei Attribute an gebunden werden:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Ereignisbehandlung

Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit. Für ein HTML-Element Attribut `on{event}` mit dem Namen ( `onclick` z `onsubmit`. b. und) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler. Der Name des Attributs ist immer mit " [ @on{Event}](xref:mvc/views/razor#onevent)" formatiert.

Der folgende Code Ruft die `UpdateHeading` -Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Der folgende Code Ruft die `CheckChanged` -Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben. Es ist nicht erforderlich, manuell aufzurufen `StateHasChanged()`. Ausnahmen werden protokolliert, wenn Sie auftreten.

Im folgenden Beispiel wird asynchron aufgerufen, `UpdateHeading` wenn die Schaltfläche ausgewählt wird:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>Ereignis Argument Typen

Für einige Ereignisse sind Ereignis Argument Typen zulässig. Wenn der Zugriff auf einen dieser Ereignis Typen nicht erforderlich ist, ist er im Methoden Aufrufvorgang nicht erforderlich.

Die unterstützten [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) sind in der folgenden Tabelle aufgeführt.

| event | Klasse |
| ----- | ----- |
| Zwischenablage        | `ClipboardEventArgs` |
| Hinein             | `DragEventArgs`&ndash; und`DataTransferItem`halten gezogene Elementdaten. `DataTransfer` |
| Fehler            | `ErrorEventArgs` |
| Fokus            | `FocusEventArgs`Beinhaltet keine unter `relatedTarget`Stützung für. &ndash; |
| `<input>` -Änderung | `ChangeEventArgs` |
| Tastatur         | `KeyboardEventArgs` |
| Maus            | `MouseEventArgs` |
| Mauszeiger    | `PointerEventArgs` |
| Mausrad      | `WheelEventArgs` |
| Status         | `ProgressEventArgs` |
| Toucheingabe            | `TouchEventArgs`&ndash; stellteineneinzelnenKontaktpunktaufeinem`TouchPoint` Berührungs sensiblen Gerät dar. |

Informationen zu den Eigenschaften und dem Ereignis Behandlungs Verhalten der Ereignisse in der vorangehenden Tabelle finden Sie unter [EventArgs classes in the Reference Source (ASPNET/aspnetcore Release/3.0-preview9 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Lambdaausdrücke

Lambda-Ausdrücke können auch verwendet werden:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen. Im folgenden Beispiel werden drei Schaltflächen erstellt, die jeweils `UpdateHeading` aufrufen, wenn ein Ereignis`MouseEventArgs`Argument () und ihre Schalt`buttonNumber`Flächen Nummer () übergeben werden, wenn es in der Benutzeroberfläche ausgewählt wird:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Verwenden Sie die Schleifenvariable (`i`) nicht in einer `for` Schleife direkt in einem Lambda-Ausdruck. Andernfalls wird die gleiche Variable von allen Lambda Ausdrücken `i`verwendet, die bewirken, dass der Wert in allen Lambdas gleich ist. Erfassen Sie den Wert stets in einer lokalen Variablen`buttonNumber` (im vorherigen Beispiel), und verwenden Sie Sie dann.

### <a name="eventcallback"></a>EventCallback

Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein unter&mdash;geordnetes Komponenten Ereignis auftritt, `onclick` z. b. Wenn ein Ereignis im untergeordneten auftritt. Um Ereignisse über Komponenten hinweg verfügbar zu machen `EventCallback`, verwenden Sie eine. Eine übergeordnete Komponente kann eine Rückruf Methode zu einer untergeordneten Komponente `EventCallback`zuweisen.

Die `ChildComponent` in der Beispiel-App veranschaulicht, wie der `onclick` Handler einer Schaltfläche so eingerichtet wird `EventCallback` , dass er einen Delegaten aus dem des `ParentComponent`Beispiels empfängt. Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Der `ParentComponent` legt die- `ShowMessage` Methode `EventCallback<T>` des untergeordneten-Elements auf fest:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Wenn die Schaltfläche in der `ChildComponent`folgenden Option ausgewählt ist:

* Die `ParentComponent` -`ShowMessage` Methode wird aufgerufen. `messageText`wird aktualisiert und in der `ParentComponent`angezeigt.
* Ein-`ShowMessage`Befehl istinder-MethodedesRückrufs(`StateHasChanged` ) nicht erforderlich. `StateHasChanged`wird automatisch aufgerufen, um den erneut `ParentComponent`zu starten, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.

`EventCallback`und `EventCallback<T>` erlauben asynchrone Delegaten. `EventCallback<T>`ist stark typisiert und erfordert einen bestimmten Argumenttyp. `EventCallback`ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

Rufen Sie `EventCallback` ein `EventCallback<T>` oder `InvokeAsync` mit auf, <xref:System.Threading.Tasks.Task>und erwarten Sie Folgendes:

```csharp
await callback.InvokeAsync(arg);
```

Verwenden `EventCallback` Sie `EventCallback<T>` und für die Parameter für die Ereignis Behandlung und die bindungskomponente.

Bevorzugen Sie die stark `EventCallback<T>` typisierte `EventCallback`. `EventCallback<T>`bietet eine bessere Fehlermeldung für Benutzer der Komponente. Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional. Verwenden `EventCallback` Sie, wenn kein Wert an den Rückruf übermittelt wird.

## <a name="chained-bind"></a>Verkettete Bindung

Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente. Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.

Eine verkettete Bindung kann nicht `@bind` mit Syntax im-Element der Seite implementiert werden. Der Ereignishandler und der Wert müssen separat angegeben werden. Eine übergeordnete Komponente kann jedoch Syntax mit `@bind` dem-Parameter der Komponente verwenden.

Die folgende `PasswordField` Komponente (*PasswordField. Razor*):

* Legt den `<input>` Wert eines Elements auf eine `Password` Eigenschaft fest.
* Macht Änderungen der `Password` Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:

* Erstellen Sie ein Unterstützungs Feld `Password` für`password` (im folgenden Beispielcode).
* Führen Sie die Überprüfungen oder Trap Fehler `Password` im Setter aus.

Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a>Verweise auf Komponenten erfassen

Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, `Show` z `Reset`. b. oder. So erfassen Sie einen Komponenten Verweis:

* Fügen Sie [@ref](xref:mvc/views/razor#ref) der untergeordneten Komponente ein Attribut hinzu.
* Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Wenn die Komponente gerendert wird `loginDialog` , wird das Feld mit `MyLoginDialog` der untergeordneten Komponenteninstanz aufgefüllt. Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.

> [!IMPORTANT]
> Die `loginDialog` -Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und `MyLoginDialog` die Ausgabe das-Element enthält. Bis zu diesem Punkt sind keine Verweise mehr vorhanden. Verwenden Sie die-Methode oder `OnAfterRenderAsync` `OnAfterRender` die-Methode, um Komponenten Verweise nach dem Rendering der Komponente zu bearbeiten.

Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion Komponenten Verweise werden nicht an JavaScript-&mdash;Code übermittelt, sondern nur in .NET-Code.

> [!NOTE]
> Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren. Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben. Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.

## <a name="invoke-component-methods-externally-to-update-state"></a>Komponenten Methoden extern aufrufen, um den Status zu aktualisieren

Blazor verwendet einen `SynchronizationContext` , um einen einzelnen logischen Ausführungs Thread zu erzwingen. Die Lebenszyklus Methoden einer Komponente und alle Ereignis Rückrufe, die von blazor ausgelöst werden, werden `SynchronizationContext`für dieses ausgeführt. Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, `InvokeAsync` verwenden Sie die- `SynchronizationContext`Methode, die an blazor weitergeleitet wird.

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

Verwendung des `NotifierService` zum Aktualisieren einer Komponente:

```cshtml
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

Im vorherigen Beispiel `NotifierService` Ruft die- `OnNotify` Methode der Komponente `SynchronizationContext`außerhalb von blazor auf. `InvokeAsync`wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Verwenden \@Sie den Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.

Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss der diffalling-Algorithmus von blazor entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen. Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.

Betrachten Sie das folgende Beispiel:

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

Der Inhalt `People` der Auflistung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern. Wenn die Komponente erneut ausgeführt wird, `<DetailsEditor>` kann sich die Komponente ändern, `Details` um unterschiedliche Parameterwerte zu erhalten. Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet. In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.

Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden. `@key`bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:

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

Wenn sich `People` die Auflistung ändert, behält der diff-Algorithmus die Zuordnung `<DetailsEditor>` zwischen Instanzen `person` und Instanzen bei:

* Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die `<DetailsEditor>` entsprechende-Instanz aus der Benutzeroberfläche entfernt. Andere Instanzen bleiben unverändert.
* Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird `<DetailsEditor>` eine neue-Instanz an der entsprechenden Position eingefügt. Andere Instanzen bleiben unverändert.
* Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.

In einigen Szenarien minimiert die Verwendung `@key` von die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. die Fokusposition.

> [!IMPORTANT]
> Schlüssel sind für jedes Containerelement oder jede Komponente lokal. Schlüssel werden nicht global über das Dokument hinweg verglichen.

### <a name="when-to-use-key"></a>Verwendung \@des Schlüssels

In der Regel ist es sinnvoll, `@key` immer dann zu verwenden, wenn eine Liste gerendert `@foreach` wird (z. b. in einem-Block `@key`) und ein geeigneter Wert zum Definieren von vorhanden ist.

Sie können auch verwenden `@key` , um zu verhindern, dass blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Wenn `@currentPerson` sich ändert, `@key` erzwingt die Attribut Direktive, dass blazor den gesamten `<div>` und seine Nachfolger verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu erstellt. Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand `@currentPerson` beibehalten wird, wenn Änderungen vorgenommen werden.

### <a name="when-not-to-use-key"></a>Verwendung \@des Schlüssels nicht

Bei der Verwendung von ist eine Leistungssteigerung zu `@key`erzielen. Die Leistungskosten sind nicht groß, sondern nur `@key` , wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.

Auch wenn `@key` nicht verwendet wird, behält blazor das untergeordnete Element und die Komponenten Instanzen so weit wie möglich bei. Der einzige Vorteil bei der `@key` Verwendung von besteht darin, zu steuern, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt den diffor-Algorithmus, der die Zuordnung auswählt.

### <a name="what-values-to-use-for-key"></a>Welche Werte für \@Key verwendet werden sollen

Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:

* Modell Objektinstanzen (z. b. `Person` eine-Instanz, wie im vorherigen Beispiel). Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.
* Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom `int`Typ `string`, oder `Guid`).

Stellen Sie sicher, dass `@key` die für verwendeten Werte nicht kollidieren. Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann. Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.

## <a name="lifecycle-methods"></a>Lebenszyklusmethoden

`OnInitializedAsync`und `OnInitialized` führen Code aus, um die Komponente zu initialisieren. Verwenden `OnInitializedAsync` Sie zum Ausführen eines asynchronen Vorgangs das- `await` Schlüsselwort und das-Schlüsselwort für den Vorgang:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Verwenden `OnInitialized`Sie für einen synchronen Vorgang Folgendes:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync`und `OnParametersSet` werden aufgerufen, wenn eine Komponente Parameter von ihrem übergeordneten Element empfangen hat und die Werte Eigenschaften zugewiesen werden. Diese Methoden werden nach der Initialisierung der Komponente und jedes Mal, wenn die Komponente gerendert wird, ausgeführt:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`und `OnAfterRender` werden aufgerufen, nachdem eine Komponente das Rendering abgeschlossen hat. Element-und Komponenten Verweise werden an diesem Punkt aufgefüllt. Verwenden Sie diese Phase, um zusätzliche Initialisierungs Schritte unter Verwendung des gerenderten Inhalts auszuführen, z. b. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die auf den gerenderten Dom

`OnAfterRender`*wird nicht aufgerufen, wenn die Vorab Anmeldung auf dem Server erfolgt.*

Der `firstRender` -Parameter `OnAfterRenderAsync` für `OnAfterRender` und ist:

* Wird zum `true` ersten Mal aufgerufen, wenn die Komponenteninstanz aufgerufen wird.
* Stellt sicher, dass die Initialisierungs Arbeit nur einmal ausgeführt wird.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>Unvollständige Async-Aktionen bei Rendering behandeln

Asynchrone Aktionen, die in Lebenszyklus Ereignissen ausgeführt werden, wurden möglicherweise nicht abgeschlossen, bevor die Komponente gerendert Objekte können oder `null` nicht vollständig mit Daten aufgefüllt werden, während die Lebenszyklus Methode ausgeführt wird. Stellen Sie Renderinglogik bereit, um zu bestätigen, dass Objekte initialisiert Stellen Sie Platzhalter-Benutzeroberflächen Elemente (z. b. eine Lade Nachricht `null`) dar, während-Objekte sind.

In der `FetchData` -Komponente der blazor-Vorlagen `OnInitializedAsync` wird überschrieben, um Vorhersagedaten asynchron`forecasts`zu empfangen (). Wenn `forecasts`den Wert hat,wirddemBenutzereineMeldungzumLadenangezeigt.`null` Nachdem der `Task` von `OnInitializedAsync` zurückgegebene abgeschlossen wurde, wird die Komponente mit dem aktualisierten Zustand erneut ausgeführt.

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Ausführen von Code vor dem Festlegen von Parametern

`SetParameters`kann überschrieben werden, um Code vor dem Festlegen von Parametern auszuführen:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Wenn `base.SetParameters` nicht aufgerufen wird, kann der benutzerdefinierte Code den Wert eingehender Parameter in beliebiger Weise interpretieren. Beispielsweise müssen die eingehenden Parameter nicht den Eigenschaften der Klasse zugewiesen werden.

### <a name="suppress-refreshing-of-the-ui"></a>Aktualisierung der Benutzeroberfläche unterdrücken

`ShouldRender`kann überschrieben werden, um die Aktualisierung der Benutzeroberfläche zu unterdrücken. Wenn die Implementierung zurück `true`gibt, wird die Benutzeroberfläche aktualisiert. Auch wenn `ShouldRender` überschrieben wird, wird die Komponente immer anfänglich gerendert.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Komponenten Beseitigung mit iverwerf

Wenn eine Komponente implementiert <xref:System.IDisposable>, wird die verwerfen- [Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird. Die folgende Komponente verwendet `@implements IDisposable` die- `Dispose` Methode und die-Methode:

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Routing

Das Routing in blazor wird durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der APP erreicht.

Wenn eine Razor-Datei mit `@page` einer-Direktive kompiliert wird, erhält die generierte <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> -Klasse einen, der die Routen Vorlage angibt. Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.

Mehrere Routen Vorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Routen Parameter

Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in `@page` der-Direktive bereitgestellt wird. Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.

Weiterleitungs *Parameter Komponente*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

Optionale Parameter werden nicht unterstützt, `@page` daher werden im obigen Beispiel zwei-Direktiven angewendet. Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter. Die zweite `@page` Anweisung übernimmt den `{text}` Routen Parameter und `Text` weist den Wert der-Eigenschaft zu.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Basisklassen Vererbung für eine "Code Behind"-Funktion

Komponenten Dateien mischen HTML-Markup C# und Verarbeitungs Code in derselben Datei. Die `@inherits` -Direktive kann verwendet werden, um blazor-apps eine "Code Behind"-Funktion bereitzustellen, die das Komponenten Markup von der Code Verarbeitung trennt.

Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) zeigt, wie eine Komponente eine Basisklasse erben `BlazorRocksBase`kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.

*Pages/blazorrocks. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Die Basisklasse sollte von `ComponentBase`abgeleitet werden.

## <a name="import-components"></a>Importieren von Komponenten

Der Namespace einer mit Razor erstellten Komponente basiert auf:

* Der des Projekts `RootNamespace`.
* Der Pfad vom Projektstamm zur Komponente. Beispielsweise `ComponentsSample/Pages/Index.razor` befindet sich im-Namespace `ComponentsSample.Pages`. Komponenten folgen C# den namens Bindungs Regeln. Im Fall von " *Index. Razor*" befinden sich alle Komponenten im gleichen Ordner, auf den *Seiten*und im übergeordneten Ordner " *componentssample*" im Gültigkeitsbereich.

Komponenten, die in einem anderen Namespace definiert sind, können mit der [ \@using](xref:mvc/views/razor#using) -Direktive von Razor in den Gültigkeitsbereich

Wenn eine andere Komponente `NavMenu.razor`,, im Ordner `ComponentsSample/Shared/`vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using` Anweisung verwendet werden:

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, sodass die [ using-Direktivenichtmehrbenötigtwird:\@](xref:mvc/views/razor#using)

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> Die `global::` Qualifizierung wird nicht unterstützt.
>
> Das Importieren von Komponenten mit `using` Alias Anweisungen ( `@using Foo = Bar`z. b.) wird nicht unterstützt.
>
> Teilweise qualifizierte Namen werden nicht unterstützt. Beispielsweise wird das `@using ComponentsSample` Hinzufügen von `NavMenu.razor` und `<Shared.NavMenu></Shared.NavMenu>` verweisen mit nicht unterstützt.

## <a name="conditional-html-element-attributes"></a>Attribute für bedingtes HTML-Element

HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert. Wenn der Wert oder `false` `null`ist, wird das Attribut nicht gerendert. Wenn der Wert ist `true`, wird das Attribut minimiert gerendert.

Im folgenden Beispiel wird von `IsCompleted` bestimmt, `checked` ob im Markup des Elements gerendert wird:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Wenn `IsCompleted`den Wert hat,wirddasKontrollkästchenwiefolgtgerendert:`true`

```html
<input type="checkbox" checked />
```

Wenn `IsCompleted`den Wert hat,wirddasKontrollkästchenwiefolgtgerendert:`false`

```html
<input type="checkbox" />
```

Weitere Informationen finden Sie unter <xref:mvc/views/razor>.

## <a name="raw-html"></a>Unformatierte HTML

Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden. Um unformatierten HTML-Code zu erstellen, umschließen Sie den HTML-Inhalt in einen `MarkupString` Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.

> [!WARNING]
> Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.

Im folgenden Beispiel wird gezeigt, `MarkupString` wie der-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Komponenten mit Vorlagen

Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können. Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind. Einige Beispiele sind:

* Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.
* Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.

### <a name="template-parameters"></a>Vorlagenparameter

Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert. Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar. `RenderFragment<T>`nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.

`TableTemplate`Zulieferern

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können Sie die Vorlagen Parameter mit untergeordneten Elementen angeben, die mit den Namen`TableHeader` der `RowTemplate` Parameter (und im folgenden Beispiel) identisch sind:

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>Vorlagen Kontext Parameter

Komponenten Argumente vom Typ `RenderFragment<T>` , `@context.PetId`die als-Elemente übergeben werden, `context` verfügen über einen impliziten Parameter mit dem Namen (z. b. aus dem vorangehenden Codebeispiel) `Context` . Sie können den Parameternamen jedoch mit dem-Attribut für das untergeordnete gewisses. Im folgenden Beispiel gibt das `RowTemplate` -Attribut des-Elements den `pet` - `Context` Parameter an:

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

Alternativ können Sie das `Context` -Attribut für das Component-Element angeben. Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter. Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element). Im folgenden Beispiel wird das `Context` -Attribut `TableTemplate` im-Element angezeigt und gilt für alle Vorlagen Parameter:

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>Generisch typisierte Komponenten

Auf Vorlagen basierende Komponenten werden oft generisch typisiert. Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` von Werten verwendet werden. Zum Definieren einer generischen Komponente verwenden Sie `@typeparam` die-Direktive, um Typparameter anzugeben:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt. Im folgenden Beispiel wird `TItem="Pet"` der-Typ angegeben:

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Kaskadierende Werte und Parameter

In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind. Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen. Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.

### <a name="theme-example"></a>Designbeispiel

Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo` -Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.

*Uithmeclasses/themerfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen. Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.

Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext `@Body` der Eigenschaft bilden. `ButtonClass`wird `btn-success` in der Layoutkomponente der Wert zugewiesen. Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierende Objekt nutzen.

`CascadingValuesParametersLayout`Zulieferern

```cshtml
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

Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]` -Attributs. Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.

In der Beispiel-App bindet `CascadingValuesParametersTheme` die Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter. Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.

`CascadingValuesParametersTheme`Zulieferern

```cshtml
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

Wenn Sie mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren möchten, stellen Sie `Name` eine eindeutige Zeichen `CascadingValue` Folge für jede Komponente `CascadingParameter`und ihre entsprechende bereit. Im folgenden Beispiel werden unterschiedliche `CascadingValue` Instanzen von `MyCascadingType` anhand des Namens von zwei Komponenten kaskadiert:

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:

```cshtml
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

Die Beispiel-App verfügt `ITab` über eine Schnittstelle, die Registerkarten implementieren:

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet` -Komponente, die mehrere `Tab` Komponenten enthält:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Die untergeordneten `TabSet` KomponentenwerdennichtexplizitalsParameterandas-`Tab` Element übermittelt. Stattdessen sind die unter `Tab` geordneten Komponenten Teil des untergeordneten Inhalts `TabSet`von. Allerdings muss `TabSet` immer noch über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code `TabSet` erforderlich ist, *kann sich die Komponente selbst als kaskadierenden Wert bereitstellen* , der dann `Tab` von den Nachfolger Komponenten abgerufen wird.

`TabSet`Zulieferern

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

Die Nachfolger `TabSet` `Tab` `TabSet` Komponenten erfassen den enthaltenden als kaskadierenden Parameter, sodass sich die Komponenten der-und-Koordinate, auf der die Registerkarte aktiv ist, selbst hinzufügen. `Tab`

`Tab`Zulieferern

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor-Vorlagen

Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden. Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

Im folgenden Beispiel wird veranschaulicht, wie `RenderFragment` - `RenderFragment<T>` Werte und-Werte angegeben und Vorlagen direkt in einer-Komponente dargestellt werden. Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Gerenderte Ausgabe des vorangehenden Codes:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Manuelle rendertreebuilder-Logik

`Microsoft.AspNetCore.Components.RenderTree`stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.

> [!NOTE]
> Die Verwendung `RenderTreeBuilder` von zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.

Sehen Sie sich `PetDetails` die folgende Komponente an, die manuell in eine andere Komponente integriert werden kann:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel generiert die-Schleife in der `CreateComponent` -Methode drei `PetDetails` -Komponenten. Beim Aufrufen `RenderTreeBuilder` von Methoden zum Erstellen der Komponenten`OpenComponent` ( `AddAttribute`und) sind Sequenznummern Quell Codezeilen Nummern. Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen. Wenn Sie eine Komponente mit `RenderTreeBuilder` Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern. **Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.** Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent`Zulieferern

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.

Blazor `.razor` -Dateien werden immer kompiliert. Dies ist möglicherweise ein großer Vorteil `.razor` für, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*. Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.

Sehen Sie sich die `.razor` folgende einfache Datei an:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Der vorangehende Code wird in etwa wie folgt kompiliert:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Wenn der Code zum ersten Mal ausgeführt wird, wenn `someFlag` `true`den Wert hat, empfängt der Generator Folgendes:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | Erster  |
| 1        | Textknoten | Zweimal |

Stellen Sie sich vor, und das Markup wird wieder gerendert. `false` `someFlag` Dieses Mal empfängt der Generator Folgendes:

| Sequenz | Typ       | Daten   |
| :------: | ---------- | :----: |
| 1        | Textknoten  | Zweimal |

Wenn die Laufzeit einen Diff-Vorgang ausführt, wird angezeigt, dass das `0` Element in der Sequenz entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:

* Entfernen Sie den ersten Textknoten.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Was schief geht, wenn Sie Programm gesteuert Sequenznummern generieren

Angenommen, Sie haben die folgende Logik des Renderstruktur-Generators geschrieben:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Nun lautet die erste Ausgabe wie folgt:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | Erster  |
| 1        | Textknoten | Zweimal |

Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind. `someFlag`befindet `false` sich auf dem zweiten Rendering, und die Ausgabe lautet:

| Sequenz | Typ      | Daten   |
| :------: | --------- | ------ |
| 0        | Textknoten | Zweimal |

Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:

* Ändern Sie den Wert für den ersten Textknoten `Second`in.
* Entfernen Sie den zweiten Textknoten.

Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo `if/else` die Verzweigungen und Schleifen im ursprünglichen Code vorhanden waren. Dies führt **doppelt so lange** wie zuvor zu einem diff.

Dies ist ein sehr einfaches Beispiel. In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer. Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.

#### <a name="guidance-and-conclusions"></a>Anleitungen und Schlussfolgerungen

* Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.
* Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.
* Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik. Bevorzugen `.razor` von Dateien und zulassen, dass der Compiler die Sequenznummern behandelt.
* Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen. Die Anfangswerte und Lücken sind irrelevant. Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen. 
* Blazor verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden. Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und blazor bietet den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler `.razor` erstellt, die Dateien erstellen.

## <a name="localization"></a>Lokalisierung

Blazor-Server-apps werden mithilfe der [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert. Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.

Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:

* [Cookies](#cookies)
* [Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur](#provide-ui-to-choose-the-culture)

Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.

### <a name="cookies"></a>Cookies

Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten. Das Cookie wird von der `OnGet` -Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt. Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen. 

Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann. Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden. Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.

Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird. Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.

Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann. Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der blazor-Server-App:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Die Lokalisierung wird in der APP behandelt:

1. Der Browser sendet eine anfängliche http-Anforderung an die app.
1. Die Kultur wird von der Lokalisierungs Middleware zugewiesen.
1. Die `OnGet` -Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.
1. Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive blazor-Server Sitzung zu erstellen.
1. Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.
1. Die blazor-Server Sitzung beginnt mit der richtigen Kultur.

## <a name="provide-ui-to-choose-the-culture"></a>Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur

Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen. Der Prozess ähnelt der Vorgehensweise in einer Web-App, wenn ein Benutzer versucht, auf eine sichere&mdash;Ressource zuzugreifen, auf die der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück an die ursprüngliche Ressource umgeleitet wird. 

Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller. Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.

Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Verwenden Sie `LocalRedirect` das Aktions Ergebnis, um offene Umleitungen zu verhindern. Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Verwenden von .net-Lokalisierungs Szenarien in blazor-apps

Innerhalb von blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:

* . NET-Ressourcensystem
* Kulturspezifische Zahlen-und Datums Formatierung

Die Funktion von `@bind` blazor führt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch. Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .

Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:

* `IStringLocalizer<>`wird in blazor-apps *unterstützt* .
* `IHtmlLocalizer<>`die `IViewLocalizer<>`Lokalisierung von-,-und-Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in blazor-apps **nicht unterstützt** .

Weitere Informationen finden Sie unter <xref:fundamentals/localization>.

## <a name="scalable-vector-graphics-svg-images"></a>SVG-Bilder (Scalable Vector Graphics)

Da blazor HTML rendert, werden vom Browser unterstützte Bilder, einschließlich der SVG-Bilder (Scalable Vector Graphics *) (SVG*), `<img>` über das-Tag unterstützt:

```html
<img alt="Example image" src="some-image.svg" />
```

Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt. Wenn Sie ein `<svg>` Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt. Beispielsweise `<use>` werden Tags derzeit nicht beachtet und können `@bind` nicht mit einigen SVG-Tags verwendet werden. Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/server>&ndash; Enthält Anleitungen zum Entwickeln von blazor-Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.
