---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 0da0d83a4fde7b753a84bf05d3a9284776f2881f
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213349"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Erstellen und Verwenden von ASP.net Core Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Blazor-apps werden mithilfe von *Komponenten*erstellt. Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular. Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist. Komponenten sind flexibel und leicht zu gestalten. Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.

## <a name="component-classes"></a>Komponenten Klassen

Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert. Eine Komponente in blazor wird formal als *Razor-Komponente*bezeichnet.

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

Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut. Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen am Dokumentobjektmodell des Browsers (DOM) an.

Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden. Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* . Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.

In der Regel wird der Namespace einer Komponente aus dem Stamm Namespace der APP und dem Speicherort (Ordner) der Komponente in der APP abgeleitet. Wenn der Stamm Namespace der APP `BlazorApp` ist und sich die `Counter` Komponente im Ordner " *pages* " befindet:

* Der Namespace der `Counter` Komponente ist `BlazorApp.Pages`.
* Der voll qualifizierte Typname der Komponente ist `BlazorApp.Pages.Counter`.

Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components) .

Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports Razor* -Datei der APP hinzu. Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `BlazorApp`ist:

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrieren von Komponenten in Razor Pages und MVC-apps

Razor-Komponenten können in Razor Pages-und MVC-Apps integriert werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab vorgerendert werden.

Wenn Sie eine Razor Pages-oder MVC-App zum Hosten von Razor-Komponenten vorbereiten möchten, befolgen Sie die Anweisungen im Abschnitt *integrieren von Razor-Komponenten in Razor Pages und MVC-apps* des <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps> Artikels.

Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace, der den Ordner darstellt, entweder der Seite/Ansicht oder der Datei " *_ViewImports. cshtml* " hinzu. Siehe folgendes Beispiel:

* Ändern Sie `MyAppNamespace` in den Namespace der app.
* Wenn ein Ordner mit dem Namen *Components* nicht zum Speichern der Komponenten verwendet wird, ändern Sie `Components` in den Ordner, in dem sich die Komponenten befinden.

```csharp
@using MyAppNamespace.Components
```

Die Datei " *_ViewImports. cshtml* " befindet sich im Ordner " *pages* " einer Razor Pages-APP oder im Ordner " *views* " einer MVC-app.

Verwenden Sie das `Component`-taghilfsprogramm, um eine Komponente aus einer Seite oder Sicht zu Rendering:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss. Beispielsweise können Sie einen Wert für `IncrementAmount` angeben, da der Typ der `IncrementAmount` ein `int`ist, bei dem es sich um einen primitiven Typ handelt, der vom JSON-Serialisierungsprogramm unterstützt wird.

`RenderMode` konfiguriert, ob die Komponente Folgendes hat:

* Wird in die Seite vorab übernommen.
* Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.

| `RenderMode`        | BESCHREIBUNG |
| ------------------- | ----------- |
| `ServerPrerendered` | Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine blazor-Server-app. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet. |
| `Server`            | Rendert einen Marker für eine blazor-Server-app. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer blazor-App verwendet. |
| `Static`            | Rendert die Komponente in statischem HTML-Format. |

Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall. Komponenten können keine Ansichts-und Seiten spezifischen Szenarien verwenden, wie z. b. partielle Sichten und Abschnitte. Um die Logik der Teilansicht in einer Komponente zu verwenden, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.

Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.

Weitere Informationen zum Rendern von Komponenten, zum Komponenten Status und zum `Component`-taghilfsprogramm finden Sie in den folgenden Artikeln:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>

## <a name="tag-helpers-arent-used-in-components"></a>Taghilfsprogramme werden in Komponenten nicht verwendet.

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden*in Razor-Komponenten (Razor* -Dateien) nicht unterstützt. Um taghilfsobjekte in blazor bereitzustellen, erstellen Sie eine-Komponente mit der gleichen Funktionalität wie das taghilfsprogramm, und verwenden Sie stattdessen die-Komponente.

## <a name="use-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Bei der Attribut Bindung wird Groß-/Kleinschreibung Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.

Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent` Instanz:

```razor
<HeadingComponent />
```

*Komponenten/headingcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist. Durch das Hinzufügen einer `@using`-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung entfernt wird.

## <a name="component-parameters"></a>Komponentenparameter

Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

*Komponenten/childcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft der `ChildComponent`fest.

*Pages/para Component. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a>Untergeordneter Inhalt

Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden. Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.

Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt. Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll. Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`des Bootstrap-Panels gerendert.

*Komponenten/childcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Die Eigenschaft, die den `RenderFragment` Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.

Der `ParentComponent` in der Beispiel-App kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>` Tags platziert wird.

*Pages/para Component. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

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

## <a name="data-binding"></a>Datenbindung

Die Datenbindung an beide Komponenten und DOM-Elemente erfolgt mit dem [`@bind`](xref:mvc/views/razor#bind) -Attribut. Im folgenden Beispiel wird eine `CurrentValue`-Eigenschaft an den Wert des Textfelds gebunden:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Wenn das Textfeld den Fokus verliert, wird der Wert der Eigenschaft aktualisiert.

Das Textfeld wird nur dann in der Benutzeroberfläche aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf das Ändern des Eigenschafts Werts. Da Komponenten nach der Ausführung des Ereignishandlercodes selbst gerenden werden, werden Eigenschafts Aktualisierungen in der Benutzeroberfläche *normalerweise* sofort nach dem Auslösen eines Ereignis Handlers

Die Verwendung von `@bind` mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im wesentlichen folgendem:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Wenn die Komponente gerendert wird, stammt der `value` des Eingabe Elements aus der `CurrentValue`-Eigenschaft. Wenn der Benutzer das Textfeld eingibt und den Element Fokus ändert, wird das `onchange`-Ereignis ausgelöst, und die `CurrentValue`-Eigenschaft wird auf den geänderten Wert festgelegt. In der Realität ist die Codegenerierung komplexer, da `@bind` Fälle behandelt, in denen Typkonvertierungen durchgeführt werden. Im Prinzip ordnet `@bind` den aktuellen Wert eines Ausdrucks einem `value`-Attribut zu und behandelt Änderungen mithilfe des registrierten Handlers.

Zusätzlich zur Behandlung von `onchange` Ereignissen mit `@bind` Syntax kann eine Eigenschaft oder ein Feld mit anderen Ereignissen gebunden werden, indem ein [`@bind-value`](xref:mvc/views/razor#bind) Attribut mit einem `event` Parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)) angegeben wird. Im folgenden Beispiel wird die `CurrentValue`-Eigenschaft für das `oninput`-Ereignis gebunden:

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.

im vorherigen Beispiel wird `@bind-value` gebunden:

* Der angegebene Ausdruck (`CurrentValue`) zum `value` Attribut des Elements.
* Ein Änderungs Ereignis Delegat für das Ereignis, das durch `@bind-value:event`angegeben wird.

### <a name="unparsable-values"></a>Nicht zu erteilbare Werte

Wenn ein Benutzer einen nicht zu erteilbaren Wert für ein Daten gebundene Element bereitstellt, wird der nicht teilbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungs Ereignis ausgelöst wird.

Nehmen Sie das folgende Szenario als Beispiel:

* Ein `<input>`-Element ist an einen `int` Typ mit einem Anfangswert von `123`gebunden:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Der Benutzer aktualisiert den Wert des-Elements auf `123.45` auf der Seite und ändert den Element Fokus.

Im vorangehenden Szenario wird der Wert des Elements auf `123`zurückgesetzt. Wenn der Wert `123.45` zugunsten des ursprünglichen Werts `123`abgelehnt wird, erkennt der Benutzer, dass sein Wert nicht akzeptiert wurde.

Standardmäßig gilt die Bindung für das `onchange` Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`). Verwenden Sie `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`, um ein anderes Ereignis festzulegen. Für das `oninput`-Ereignis (`@bind-value:event="oninput"`) tritt die Neuversion nach jedem Tastatur Strich auf, der einen nicht zu deerbaren Wert einführt. Wenn das `oninput` Ereignis mit einem `int`gebundenen Typ als Ziel verwendet wird, wird verhindert, dass ein Benutzer ein `.` Zeichen eingibt. Ein `.` Zeichen wird sofort entfernt, sodass der Benutzer sofort Feedback erhält, dass nur ganze Zahlen zulässig sind. Es gibt Szenarios, in denen das Wiederherstellen des Werts für das `oninput` Ereignis nicht ideal ist, z. b. wenn der Benutzer einen nicht zu testbaren `<input>` Wert löschen darf. Zu Alternativen gehören:

* Verwenden Sie das `oninput`-Ereignis nicht. Verwenden Sie das Standard `onchange` Ereignis (`@bind="{PROPERTY OR FIELD}"`), bei dem ein ungültiger Wert nicht wieder hergestellt wird, bis das Element den Fokus verliert.
* Binden Sie an einen Typ, der NULL-Werte zulässt, wie z. b. `int?` oder `string`, und stellen Sie eine benutzerdefinierte Logik bereit,
* Verwenden Sie eine [Formular Validierungs Komponente](xref:blazor/forms-validation), z. b. `InputNumber` oder `InputDate`. Komponenten Validierungs Komponenten verfügen über eine integrierte Unterstützung zum Verwalten Ungültiger Eingaben. Komponenten Validierungs Komponenten:
  * Ermöglicht es dem Benutzer, ungültige Eingaben bereitzustellen und Validierungs Fehler auf dem zugeordneten `EditContext`zu empfangen.
  * Zeigen Sie Validierungs Fehler in der Benutzeroberfläche an, ohne dass der Benutzer die Eingabe zusätzlicher Webform-Daten beeinträchtigt.

### <a name="globalization"></a>Globalisierung

`@bind` Werte für die Anzeige formatiert und mithilfe der Regeln der aktuellen Kultur analysiert werden.

Der Zugriff auf die aktuelle Kultur ist über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft möglich.

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

`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName> zum Auswerten und Formatieren eines Werts bereitzustellen. Die Angabe einer Kultur ist nicht empfehlenswert, wenn die Feldtypen `date` und `number` verwendet werden. `date` und `number` verfügen über eine integrierte Unterstützung für blazor, die die erforderliche Kultur bereitstellt.

Informationen zum Festlegen der Kultur des Benutzers finden Sie im Abschnitt zur [Lokalisierung](#localization) .

### <a name="format-strings"></a>Format Zeichenfolgen

Die Datenbindung funktioniert mit <xref:System.DateTime>-Format Zeichenfolgen [`@bind:format`](xref:mvc/views/razor#bind). Andere Format Ausdrücke, z. b. Währungs-oder Zahlenformate, sind zurzeit nicht verfügbar.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Im vorangehenden Code ist der Feldtyp (`type`) des `<input>` Elements standardmäßig `text`. `@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Das `@bind:format`-Attribut gibt das Datumsformat an, das auf die `value` des `<input>` Elements angewendet werden soll. Das-Format wird auch verwendet, um den Wert zu analysieren, wenn ein `onchange` Ereignis auftritt.

Es wird nicht empfohlen, ein Format für den `date` Feldtyp anzugeben, da blazor über eine integrierte Unterstützung zum Formatieren von Datumsangaben verfügt. Verwenden Sie trotz der Empfehlung nur das `yyyy-MM-dd` Datumsformat für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem `date` Feldtyp bereitgestellt wird:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>Über-zu-untergeordnete Bindung mit Komponenten Parametern

Die Bindung erkennt Komponenten Parameter, bei denen `@bind-{property}` einen Eigenschafts Wert von einer übergeordneten Komponente an eine untergeordnete Komponente binden kann. Die Bindung von einem untergeordneten zu einem übergeordneten Element wird in der Bindung zwischen untergeordneten und über [geordneten Elementen mit verketteter](#child-to-parent-binding-with-chained-bind) Bindung behandelt.

Die folgende untergeordnete Komponente (`ChildComponent`) verfügt über einen `Year` Component-Parameter und `YearChanged`-Rückruf:

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>` wird im Abschnitt [EventCallback](#eventcallback) erläutert.

Die folgende übergeordnete Komponente verwendet:

* `ChildComponent` und bindet den `ParentYear`-Parameter vom übergeordneten zum `Year`-Parameter in der untergeordneten Komponente.
* Das `onclick`-Ereignis wird verwendet, um die `ChangeTheYear`-Methode zu initiieren. Weitere Informationen finden Sie im Abschnitt [Ereignis Behandlung](#event-handling) .

```razor
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

Beim Laden des `ParentComponent` wird das folgende Markup erzeugt:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Wenn der Wert der `ParentYear`-Eigenschaft durch Auswählen der Schaltfläche im `ParentComponent`geändert wird, wird die Eigenschaft `Year` der `ChildComponent` aktualisiert. Der neue Wert `Year` wird in der Benutzeroberfläche gerendert, wenn die `ParentComponent` erneut ausgeführt wird:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Der `Year`-Parameter ist bindbar, da er über ein Begleit `YearChanged` Ereignis verfügt, das mit dem Typ des `Year` Parameters übereinstimmt.

Gemäß der Konvention ist `<ChildComponent @bind-Year="ParentYear" />` im wesentlichen Äquivalent zum Schreiben:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Im Allgemeinen kann eine Eigenschaft mithilfe `@bind-property:event` Attributs an einen entsprechenden Ereignishandler gebunden werden. Beispielsweise kann die-Eigenschaft `MyProp` an `MyEventHandler` mithilfe der folgenden beiden Attribute gebunden werden:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>Bindung zwischen untergeordneten und übergeordneten Elementen mit verketteter Bindung

Ein häufiges Szenario ist die Verkettung eines Daten gebundenen Parameters zu einem Page-Element in der Ausgabe der Komponente. Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Bindungs Ebenen gleichzeitig auftreten.

Eine verkettete Bindung kann nicht mit `@bind`-Syntax im-Element der Seite implementiert werden. Der Ereignishandler und der Wert müssen separat angegeben werden. Eine übergeordnete Komponente kann jedoch `@bind`-Syntax mit dem-Parameter der Komponente verwenden.

Die folgende `PasswordField` Komponente (*PasswordField. Razor*):

* Legt den Wert eines `<input>` Elements auf eine `Password` Eigenschaft fest.
* Macht Änderungen der `Password`-Eigenschaft für eine übergeordnete Komponente mit einem [EventCallback](#eventcallback)verfügbar.
* Verwendet das `onclick` Ereignis wird verwendet, um die `ToggleShowPassword`-Methode zu initiieren. Weitere Informationen finden Sie im Abschnitt [Ereignis Behandlung](#event-handling) .

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

Die `PasswordField` Komponente wird in einer anderen Komponente verwendet:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Zum Ausführen von Überprüfungen oder Trap Fehlern für das Kennwort im vorherigen Beispiel:

* Erstellen Sie ein Unterstützungs Feld für `Password` (`_password` im folgenden Beispielcode).
* Führen Sie die Überprüfungen oder Trap Fehler im `Password` Setter aus.

Im folgenden Beispiel wird dem Benutzer sofortiges Feedback bereitgestellt, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

### <a name="radio-buttons"></a>Optionsfelder

Informationen zum Binden an Options Felder in einem Formular finden Sie unter <xref:blazor/forms-validation#work-with-radio-buttons>.

## <a name="event-handling"></a>Behandlung von Ereignissen

Razor-Komponenten stellen Ereignis Behandlungs Funktionen bereit. Für ein HTML-Element Attribut mit dem Namen `on{EVENT}` (z. b. `onclick` und `onsubmit`) mit einem delegattypisierten Wert behandelt Razor-Komponenten den Wert des Attributs als Ereignishandler. Der Name des Attributs wird immer [`@on{EVENT}`](xref:mvc/views/razor#onevent)formatiert.

Der folgende Code Ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:

```razor
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

Der folgende Code Ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen in der Benutzeroberfläche geändert wird:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Ereignishandler können auch asynchron sein und eine <xref:System.Threading.Tasks.Task>zurückgeben. [Statehaschte](xref:blazor/lifecycle#state-changes)muss nicht manuell aufgerufen werden. Ausnahmen werden protokolliert, wenn Sie auftreten.

Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:

```razor
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

Unterstützte `EventArgs` sind in der folgenden Tabelle aufgeführt.

| Ereignis            | Klasse                | DOM-Ereignisse und-Notizen |
| ---------------- | -------------------- | -------------------- |
| Zwischenablage        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Hinein             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` und `DataTransferItem` die gezogenen Elementdaten speichern. |
| Fehler            | `ErrorEventArgs`     | `onerror` |
| Ereignis            | `EventArgs`          | *Allgemein*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Zwischenablage*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input* (Eingabe)<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medien*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Beinhaltet keine Unterstützung für `relatedTarget`. |
| Eingabe            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Tastatur         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Maus            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mauszeiger    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mausrad      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Status         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucheingabe            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` stellt einen einzelnen Kontaktpunkt auf einem Berührungs sensiblen Gerät dar. |

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [EventArgs-Klassen in der ASP.net Core Verweis Quelle (dotnet/aspnetcore Release/3.1-Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN-Webdokumentation: globaleventhandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; enthält Informationen darüber, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.

### <a name="lambda-expressions"></a>Lambdaausdrücke

Lambda-Ausdrücke können auch verwendet werden:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Häufig ist es praktisch, zusätzliche Werte zu schließen, z. b. beim Durchlaufen eines Satzes von Elementen. Im folgenden Beispiel werden drei Schaltflächen erstellt, von denen jedes `UpdateHeading` aufgerufen wird, um ein Ereignis Argument (`MouseEventArgs`) und seine Schaltflächen Nummer (`buttonNumber`) zu übergeben, wenn es in der Benutzeroberfläche ausgewählt wird:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Verwenden Sie die Schleifenvariable (`i`) in einer `for`-Schleife **nicht** direkt in einem Lambda-Ausdruck. Andernfalls wird dieselbe Variable von allen Lambda-Ausdrücken verwendet, wodurch `i`Wert in allen Lambdas gleich ist. Erfassen Sie den Wert stets in einer lokalen Variablen (`buttonNumber` im vorangehenden Beispiel), und verwenden Sie Sie dann.

### <a name="eventcallback"></a>EventCallback

Ein häufiges Szenario mit untergeordneten Komponenten ist der Wunsch, die-Methode einer übergeordneten Komponente auszuführen, wenn ein untergeordnetes Komponenten Ereignis auftritt&mdash;z. b. Wenn ein `onclick`-Ereignis im untergeordneten Element auftritt. Um Ereignisse Komponenten übergreifend verfügbar zu machen, verwenden Sie eine `EventCallback`. Eine übergeordnete Komponente kann dem `EventCallback`einer untergeordneten Komponente eine Rückruf Methode zuweisen.

Der `ChildComponent` in der Beispiel-app (*Components/childcomponent. Razor*) veranschaulicht, wie der `onclick` Handler einer Schaltfläche für den Empfang eines `EventCallback` Delegaten aus dem `ParentComponent`des Beispiels eingerichtet ist. Die `EventCallback` wird mit `MouseEventArgs`typisiert, was für ein `onclick` Ereignis von einem Peripheriegerät geeignet ist:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Der `ParentComponent` legt die `EventCallback<T>` (`OnClickCallback`) des untergeordneten Elements auf die `ShowMessage` Methode fest.

*Pages/para Component. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Wenn die Schaltfläche im `ChildComponent`ausgewählt ist:

* Die `ShowMessage`-Methode des `ParentComponent`wird aufgerufen. `_messageText` wird aktualisiert und in der `ParentComponent`angezeigt.
* In der-Methode des Rückrufs (`ShowMessage`) ist kein aufzurufende [statehasch-](xref:blazor/lifecycle#state-changes) Methode erforderlich. `StateHasChanged` wird automatisch aufgerufen, um die `ParentComponent`zu übertragen, ebenso wie untergeordnete Ereignisse die erneute Ausführung von Komponenten in Ereignis Handlern auslöst, die innerhalb des untergeordneten Elements ausgeführt werden.

`EventCallback` und `EventCallback<T>` die asynchrone Delegaten zulassen. `EventCallback<T>` ist stark typisiert und erfordert einen bestimmten Argumenttyp. `EventCallback` ist schwach typisiert und ermöglicht einen beliebigen Argumenttyp.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Rufen Sie eine `EventCallback` oder `EventCallback<T>` mit `InvokeAsync` auf, und warten Sie auf die <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Verwenden Sie `EventCallback` und `EventCallback<T>` für Ereignisverarbeitungs-und Bindungskomponenten Parameter.

Bevorzugen der stark typisierten `EventCallback<T>` über `EventCallback`. `EventCallback<T>` bietet den Benutzern der Komponente ein besseres Fehler Feedback. Ähnlich wie bei anderen Benutzeroberflächen-Ereignis Handlern ist die Angabe des Ereignis Parameters optional. Verwenden Sie `EventCallback`, wenn kein Wert an den Rückruf übermittelt wird.

### <a name="prevent-default-actions"></a>Standard Aktionen verhindern

Verwenden Sie das [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) direktivenattribut, um die Standardaktion für ein Ereignis zu verhindern.

Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und sich der Element Fokus auf einem Textfeld befindet, zeigt ein Browser normalerweise das Schlüsselzeichen im Textfeld an. Im folgenden Beispiel wird das Standardverhalten verhindert, indem das `@onkeypress:preventDefault` direktivenattribut angegeben wird. Der Wert des Leistungs Zählers wird erhöht, und der **+** Schlüssel wird nicht in den Wert des `<input>` Elements aufgezeichnet:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Das Angeben des `@on{EVENT}:preventDefault` Attributs ohne Wert entspricht `@on{EVENT}:preventDefault="true"`.

Der Wert des-Attributs kann auch ein Ausdruck sein. Im folgenden Beispiel ist `_shouldPreventDefault` ein `bool` Feld, das entweder auf `true` oder `false`festgelegt ist:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Ein Ereignishandler ist nicht erforderlich, um die Standardaktion zu verhindern. Der Ereignishandler und die Vermeidung von Standard Aktions Szenarien können unabhängig voneinander verwendet werden.

### <a name="stop-event-propagation"></a>Ereignis Weitergabe Abbrechen

Verwenden Sie das Attribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) Direktive, um die Ereignis Weitergabe zu verhindern

Im folgenden Beispiel wird durch Aktivieren des Kontrollkästchens verhindert, dass Click-Ereignisse aus dem zweiten untergeordneten `<div>` an die übergeordnete `<div>`weitergegeben werden:

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
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

Blazor verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen. Die [Lebenszyklus Methoden](xref:blazor/lifecycle) einer Komponente und alle Ereignis Rückrufe, die von blazor ausgelöst werden, werden in dieser `SynchronizationContext`ausgeführt. Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an die `SynchronizationContext`von blazor weitergeleitet wird.

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

* Registrieren Sie den Dienst in der blazor-Webassembly in `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Registrieren Sie den Dienst auf dem blazor-Server in `Startup.ConfigureServices`:

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

Im vorherigen Beispiel ruft `NotifierService` die `OnNotify` Methode der Komponente außerhalb der `SynchronizationContext`von blazor auf. `InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Verwenden Sie \@Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.

Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss der diffalling-Algorithmus von blazor entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen. Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.

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

Sie können auch `@key` verwenden, um zu verhindern, dass blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Wenn `@currentPerson` geändert wird, erzwingt die `@key` Attribute-Direktive, dass blazor den gesamten `<div>` und seine Nachfolger verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu erstellen kann. Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn `@currentPerson` geändert wird.

### <a name="when-not-to-use-key"></a>Verwendung \@Schlüssels nicht

Beim Durchlaufen der `@key`werden Kosten für die Leistung anfallen. Die Leistungskosten sind nicht groß, sondern geben nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.

Auch wenn `@key` nicht verwendet wird, behält blazor das untergeordnete Element und die Komponenten Instanzen so weit wie möglich bei. Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.

### <a name="what-values-to-use-for-key"></a>Welche Werte müssen für \@Schlüssel verwendet werden?

Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:

* Modell Objektinstanzen (z. b. eine `Person`-Instanz, wie im vorherigen Beispiel). Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.
* Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string`oder `Guid`).

Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren. Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann. Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.

## <a name="routing"></a>Routing

Das Routing in blazor wird durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der APP erreicht.

Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt. Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.

Mehrere Routen Vorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`.

*Pages/blazorroute. Razor*:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a>Routen Parameter

Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird. Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.

*Pages/RouteParameter. Razor*:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Optionale Parameter werden nicht unterstützt. Daher werden im obigen Beispiel zwei `@page` Direktiven angewendet. Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter. Die zweite `@page`-Direktive übernimmt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.

Die *catch-all-* Parameter Syntax (`*`/`**`), die den Pfad über mehrere Ordner Grenzen hinweg erfasst, wird in Razor-*Komponenten (Razor*-Komponenten) **nicht** unterstützt.

## <a name="partial-class-support"></a>Unterstützung für Teil Klassen

Razor-Komponenten werden als partielle Klassen generiert. Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:

* C#Code wird in einem [`@code`](xref:mvc/views/razor#code) -Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert. Blazor-Vorlagen definieren ihre Razor-Komponenten mithilfe dieses Ansatzes.
* C#der Code wird in einer Code Behind-Datei platziert, die als partielle Klasse definiert ist.

Das folgende Beispiel zeigt die Standard `Counter` Komponente mit einem `@code`-Block in einer APP, die aus einer blazor-Vorlage generiert wurde. HTML-Markup, Razor-Code C# und Code befinden sich in derselben Datei:

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

## <a name="templated-components"></a>Komponenten mit Vorlagen

Vorlagen Komponenten sind Komponenten, die eine oder mehrere Benutzeroberflächen Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können. Mit Vorlagen basierten Komponenten können Sie Komponenten höherer Ebene erstellen, die besser wiederverwendbar als reguläre Komponenten sind. Einige Beispiele sind:

* Eine Tabellenkomponente, mit der Benutzervorlagen für den Header, die Zeilen und die Fußzeile der Tabelle angeben können.
* Eine Listen Komponente, die es Benutzern ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste anzugeben.

### <a name="template-parameters"></a>Vorlagenparameter

Eine auf Vorlagen basierende Komponente wird durch Angabe eines oder mehrerer Komponenten Parameter vom Typ `RenderFragment` oder `RenderFragment<T>`definiert. Ein Rendering-Fragment stellt ein Segment der zu Rendering enden Benutzeroberfläche dar. `RenderFragment<T>` nimmt einen Typparameter an, der angegeben werden kann, wenn das Rendering-Fragment aufgerufen wird.

`TableTemplate` Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Wenn Sie eine auf Vorlagen basierende Komponente verwenden, können die Vorlagen Parameter mit untergeordneten Elementen angegeben werden, die den Namen der Parameter (`TableHeader` und `RowTemplate` im folgenden Beispiel) entsprechen:

```razor
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

Komponenten Argumente vom Typ `RenderFragment<T>` als Elemente übergeben, haben einen impliziten Parameter mit dem Namen `context` (z. b. aus dem vorangehenden Codebeispiel `@context.PetId`), Sie können jedoch den Parameternamen mithilfe des `Context`-Attributs für das untergeordnete Element ändern. Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate` Elements den `pet`-Parameter an:

```razor
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

Alternativ können Sie das `Context`-Attribut für das Component-Element angeben. Das angegebene `Context` Attribut gilt für alle angegebenen Vorlagen Parameter. Dies kann hilfreich sein, wenn Sie den Inhalts Parameternamen für impliziten untergeordneten Inhalt angeben möchten (ohne ein untergeordnetes Wrapping Element). Im folgenden Beispiel wird das `Context`-Attribut im `TableTemplate`-Element angezeigt und gilt für alle Vorlagen Parameter:

```razor
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

Auf Vorlagen basierende Komponenten werden oft generisch typisiert. Beispielsweise kann eine generische `ListViewTemplate` Komponente zum Rendering `IEnumerable<T>` Werte verwendet werden. Verwenden Sie zum Definieren einer generischen Komponente die [`@typeparam`](xref:mvc/views/razor#typeparam) -Direktive, um Typparameter anzugeben:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Wenn Sie generische typisierte Komponenten verwenden, wird der Typparameter nach Möglichkeit abgeleitet:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, das mit dem Namen des Typparameters übereinstimmt. Im folgenden Beispiel gibt `TItem="Pet"` den Typ an:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
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

Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`-und `RenderFragment<T>` Werte angeben und Vorlagen direkt in einer-Komponente darstellen. Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](#templated-components)übermittelt werden.

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

## <a name="manual-rendertreebuilder-logic"></a>Manuelle rendertreebuilder-Logik

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit, einschließlich des manuellen C# erbauens von Komponenten im Code.

> [!NOTE]
> Die Verwendung von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. b. ein nicht geschlossenes Markup Kennzeichen) kann zu undefiniertem Verhalten führen.

Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere-Komponente integriert werden kann:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel generiert die-Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten. Wenn Sie `RenderTreeBuilder` Methoden aufrufen, um die Komponenten (`OpenComponent` und `AddAttribute`) zu erstellen, sind die Sequenznummern Quell Codezeilen Nummern. Der blazor Difference-Algorithmus basiert auf den Sequenznummern, die unterschiedlichen Codezeilen entsprechen, nicht unterschiedlichen Aufruf aufrufen. Wenn Sie eine Komponente mit `RenderTreeBuilder`-Methoden erstellen, hart codieren Sie die Argumente für Sequenznummern. **Die Verwendung einer Berechnung oder eines Leistungs Zählers, um die Sequenznummer zu generieren, kann zu schlechter Leistung führen.** Weitere Informationen finden Sie im Abschnitt [Sequenznummern im Zusammenhang mit Codezeilen Nummern und nicht in der Ausführungsreihenfolge](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent` Komponente:

```razor
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

> [!WARNING]
> Die Typen in `Microsoft.AspNetCore.Components.RenderTree` die Verarbeitung der *Ergebnisse* von Renderingvorgängen zulassen. Hierbei handelt es sich um interne Details der blazor-Framework-Implementierung. Diese Typen sollten als *instabil* eingestuft werden und in zukünftigen Versionen geändert werden können.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Sequenznummern beziehen sich auf Codezeilen Nummern und keine Ausführungsreihenfolge.

Blazor-`.razor` Dateien werden immer kompiliert. Dies ist möglicherweise ein großer Vorteil für `.razor`, da der Kompilierungs Schritt zum Einfügen von Informationen verwendet werden kann, die die APP-Leistung zur Laufzeit verbessern.

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*. Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. Diese Informationen werden von der Common Language Runtime verwendet, um effiziente Strukturunterschiede in linearer Zeit zu generieren, die wesentlich schneller ist als bei einem allgemeinen Struktur Vergleichsalgorithmus.

Sehen Sie sich die folgende Razor-Komponenten Datei ( *. Razor*) an:

```razor
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

Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true`ist:

| Sequenz | type      | Data   |
| :------: | --------- | :----: |
| 0        | Textknoten | First (Erster)  |
| 1        | Textknoten | Sekunde |

Stellen Sie sich vor, dass `someFlag` `false`wird und das Markup wieder gerendert wird. Dieses Mal empfängt der Generator Folgendes:

| Sequenz | type       | Data   |
| :------: | ---------- | :----: |
| 1        | Textknoten  | Sekunde |

Wenn die Laufzeit einen diff ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde, sodass das folgende triviale *Bearbeitungs Skript*generiert wird:

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

| Sequenz | type      | Data   |
| :------: | --------- | :----: |
| 0        | Textknoten | First (Erster)  |
| 1        | Textknoten | Sekunde |

Dieses Ergebnis ist mit dem vorherigen Fall identisch, sodass keine negativen Probleme aufgetreten sind. `someFlag` wird im zweiten Rendering `false`, und die Ausgabe lautet:

| Sequenz | type      | Data   |
| :------: | --------- | ------ |
| 0        | Textknoten | Sekunde |

Dieses Mal sieht der Vergleichsalgorithmus, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungs Skript:

* Ändern Sie den Wert für den ersten Textknoten in `Second`.
* Entfernen Sie den zweiten Textknoten.

Das Erzeugen der Sequenznummern hat alle nützlichen Informationen dazu verloren, wo die `if/else` branches und Schleifen im ursprünglichen Code vorhanden waren. Dies führt **doppelt so lange** wie zuvor zu einem diff.

Dies ist ein sehr einfaches Beispiel. In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen, insbesondere mit Schleifen, sind die Leistungseinbußen schwerer. Anstatt sofort festzustellen, welche Schleifen Blöcke oder Verzweigungen eingefügt oder entfernt wurden, muss der Vergleichsalgorithmus tief in den renderbaum Strukturen rekurdieren und in der Regel viel mehr Bearbeitungs Skripts erstellen, da er nicht informiert ist, wie die alten und neuen Strukturen Beziehung zueinander.

#### <a name="guidance-and-conclusions"></a>Anleitungen und Schlussfolgerungen

* Die APP-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.
* Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, Sie werden zur Kompilierzeit aufgezeichnet.
* Schreiben Sie keine langen Blöcke der manuell implementierten `RenderTreeBuilder` Logik. Bevorzugen Sie `.razor` Dateien, und ermöglichen Sie dem Compiler, die Sequenznummern zu behandeln. Wenn Sie manuelle `RenderTreeBuilder` Logik nicht vermeiden können, teilen Sie lange Code Blöcke in kleinere Teile auf, die `OpenRegion`/`CloseRegion` aufrufen umschließen. Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von NULL (oder einer beliebigen anderen beliebigen Zahl) neu starten können.
* Wenn Sequenznummern hart codiert sind, erfordert der Vergleichsalgorithmus nur, dass die Sequenznummern den Wert erhöhen. Die Anfangswerte und Lücken sind irrelevant. Eine berechtigte Option besteht darin, die Codezeilen Nummer als Sequenznummer zu verwenden oder von NULL zu beginnen und um Werte oder Hunderte (oder ein beliebiges bevorzugtes Intervall) zu erhöhen. 
* Blazor verwendet Sequenznummern, während andere Benutzeroberflächen-Frameworks, die Sie nicht verwenden, diese verwenden. Das diffalling ist weitaus schneller, wenn Sequenznummern verwendet werden, und Blazor hat den Vorteil eines Kompilierungs Schritts, der automatisch Sequenznummern für Entwickler erstellt, die *Razor* -Dateien erstellen.

## <a name="localization"></a>Lokalisierung

Blazor Server-apps werden mithilfe von [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert. Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.

Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:

* [Cookies](#cookies)
* [Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur](#provide-ui-to-choose-the-culture)

Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Konfigurieren des Linkers für die Internationalisierung (Blazor Webassembly)

Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas. Weitere Informationen und Anleitungen zum Steuern des linkerverhaltens finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Cookies

Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten. Das Cookie wird durch die `OnGet`-Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt. Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen. 

Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann. Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden. Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.

Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird. Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.

Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann. Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der Blazor Server-App:

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
1. Die `OnGet`-Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.
1. Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive Blazor Server-Sitzung zu erstellen.
1. Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.
1. Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.

### <a name="provide-ui-to-choose-the-culture"></a>Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur

Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen. Der Prozess ähnelt dem, was in einer Web-App geschieht, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen,&mdash;der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource umgeleitet wird. 

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
> Verwenden Sie das Ergebnis der `LocalRedirect` Aktion, um Open Redirect-Angriffe zu verhindern. Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a>Verwenden von .net-Lokalisierungs Szenarien in Blazor-apps

In Blazor-apps sind die folgenden Szenarien für die Lokalisierung und Globalisierung von .net verfügbar:

* . NET-Ressourcensystem
* Kulturspezifische Zahlen-und Datums Formatierung

die `@bind` Funktionalität von Blazorführt die Globalisierung basierend auf der aktuellen Kultur des Benutzers durch. Weitere Informationen finden Sie im Abschnitt [Datenbindung](#data-binding) .

Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:

* `IStringLocalizer<>` wird in Blazor-apps *unterstützt* .
* die Lokalisierung von `IHtmlLocalizer<>`, `IViewLocalizer<>`und Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in Blazor-apps **nicht unterstützt** .

Weitere Informationen finden Sie unter <xref:fundamentals/localization>.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von Blazor Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.
