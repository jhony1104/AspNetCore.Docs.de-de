---
title: Erstellen und Verwenden von Razor-Komponenten
author: guardrex
description: Informationen Sie zum Erstellen und Verwenden von Razor-Komponenten, einschließlich Informationen zum Binden an Daten, Verarbeiten von Ereignissen und Lebenszyklen Komponente verwalten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/components
ms.openlocfilehash: f8ac7f3844b94a162e8d1c45f80ae153d89536ee
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515562"
---
# <a name="create-and-use-razor-components"></a>Erstellen und Verwenden von Razor-Komponenten

Durch [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), und [Morné Zaayman](https://github.com/MorneZaayman)

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)). Informieren Sie sich in dem Thema [Erste Schritte](xref:razor-components/get-started) über die Voraussetzungen.

Apps für Razor-Komponenten basieren *Komponenten*. Eine Komponente ist als eigenständigen Teil der Benutzeroberfläche (UI), z. B. eine Seite, Dialogfeld oder Formular. Eine Komponente enthält die HTML-Markup und die Verarbeitungslogik zum Einfügen von Daten oder zum Reagieren auf Ereignisse der Benutzeroberfläche erforderlich. Komponenten sind flexibler und einfacher. Sie können geschachtelte, wiederverwendet und von den Projekten gemeinsam verwendet werden.

## <a name="component-classes"></a>Komponentenklassen

Komponenten werden in der Regel in Razor-Komponentendateien implementiert (*.razor*) mit einer Kombination aus C# und HTML-Markup (*.cshtml* Dateien in Blazor-apps verwendet werden).

Komponenten erstellt werden können, wird in Razor-Komponenten-apps, die mit der *.cshtml* Dateierweiterung aus, solange die Dateien identifiziert werden, als Komponente der Razor-Dateien, die mit der `_RazorComponentInclude` MSBuild-Eigenschaft. Eine app, die mit der Komponente der Razor-Vorlage erstellt z. B. gibt an, dass alle *.cshtml* Dateien unter den *Komponenten* Ordner als Komponenten der Razor-Dateien behandelt werden soll:

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

Die Benutzeroberfläche für eine Komponente ist HTML-Format definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt. Wenn eine app für Razor-Komponenten kompiliert wird, das HTML-Markup und C# Renderinglogik in eine Komponentenklasse konvertiert werden. Der Name der generierten Klasse entspricht der Name der Datei.

Mitglieder der Komponentenklasse werden definiert, einem `@functions` Block (mehr als eine `@functions` Block ist zulässig). In der `@functions` Block Komponentenstatus (Eigenschaften, Felder) wird angegeben, mit Methoden für die Behandlung von Ereignissen oder um andere Logik für die Komponente zu definieren.

Mitglieder der Komponente können dann verwendet werden, als Teil der Komponente mit der Logik des Rendern C# Ausdrücke, die mit beginnen `@`. Z. B. eine C# Feld gerendert wird, werden `@` auf den Namen des Felds. Im folgenden Beispiel ergibt und rendert:

* `_headingFontStyle` in den CSS-Eigenschaft-Wert für `font-style`.
* `_headingText` um den Inhalt der `<h1>` Element.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Nachdem die Komponente ursprünglich gerendert wurde, wird die Komponente der Render-Struktur als Reaktion auf Ereignisse erneut generiert. Razor-Komponenten die neue Rendering-Struktur mit dem vorherigen Beispiel vergleicht und wendet alle Änderungen, Model (DOM) des Browsers.

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrieren von Komponenten in Razor-Seiten und MVC-apps

Verwenden Sie Komponenten mit vorhandenen Razor-Seiten und MVC-apps. Besteht keine Notwendigkeit, vorhandene Seiten oder Ansichten mit Razor-Komponenten neu zu schreiben. Beim Rendern der Seite oder Ansicht Komponenten sind vorab gerenderte&dagger; zur gleichen Zeit. 

> [!NOTE]
> &dagger;Serverseitige Prerendering ist für apps mit Razor-Komponenten standardmäßig aktiviert. Die clientseitige Blazor apps werden Prerendering in der bevorstehenden Version von Preview 4 unterstützt. Weitere Informationen finden Sie unter [Aktualisieren von Vorlagen/Middleware für die Datei "/ MapFallbackToPage" Verwendung](https://github.com/aspnet/AspNetCore/issues/8852).

Um eine Komponente von einer Seite oder Ansicht zu rendern, verwenden Sie die `RenderComponentAsync<TComponent>` HTML-Hilfsmethode:

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

Komponenten von Seiten und Ansichten gerendert sind noch nicht interaktiv in die Version Preview 3. Auswählen einer Schaltfläche nicht z. B. Aufruf einer Methode ausgelöst wird. Eine kommenden Vorschau wird diese Einschränkung zu beheben und Hinzufügen von Unterstützung für Rendering-Komponenten, die mit der normalen Syntax von Element- und Attributnamen.

Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil "true" nicht. Komponenten können keine Sicht und seitenspezifische-Szenarien, z. B. Teilansichten und Abschnitte. Um Logik aus Teilansicht in einer Komponente, auszuklammern die Teilansicht Logik in eine Komponente zu verwenden.

## <a name="using-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten einbinden, indem sie deklarieren mithilfe der Syntax von HTML-Element. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Das folgende Markup rendert eine `HeadingComponent` (*HeadingComponent.cshtml*) Instanz:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a>Komponentenparameter

Komponenten haben *Parameter der Komponente*, die definiert werden, mithilfe von *nicht öffentlichen* Eigenschaften für die Component-Klasse ergänzt `[Parameter]`. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

Im folgenden Beispiel die `ParentComponent` legt den Wert für die `Title` Eigenschaft der `ChildComponent`:

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a>Untergeordneter Inhalt

Komponenten können den Inhalt einer anderen Komponente festlegen. Zuweisen von Komponente enthält den Inhalt zwischen die Tags, die die empfangende Komponente angeben. Z. B. eine `ParentComponent` bieten Inhalte für das Rendering durch eine untergeordnete Komponente durch die Platzierung des Inhalts in `<ChildComponent>` Tags.

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

Die untergeordnete Komponente verfügt über eine `ChildContent` dargestellten Eigenschaft eine `RenderFragment`. Der Wert des `ChildContent` befindet sich im Markup für die untergeordnete Komponente, in dem der Inhalt gerendert werden soll. Im folgenden Beispiel den Wert der `ChildContent` von der übergeordneten Komponente empfangen wird und innerhalb des Bereichs der Bootstrap `panel-body`.

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> Die Eigenschaft empfangen die `RenderFragment` Inhalt muss den Namen `ChildContent` gemäß der Konvention.

## <a name="data-binding"></a>Datenbindung

Datenbindung an Komponenten und DOM-Elemente sowohl erfolgt mithilfe der `bind` Attribut. Im folgenden Beispiel wird die `ItalicsCheck` -Eigenschaft auf des Kontrollkästchens aktiviert, Status:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

Wenn Sie dieses Kontrollkästchen aktiviert und deaktiviert ist, wird der Wert der Eigenschaft auf aktualisiert `true` und `false`bzw.

Nur, wenn die Komponente gerendert wird, nicht als Reaktion auf den Wert der Eigenschaft ändern, wird das Kontrollkästchen in der Benutzeroberfläche aktualisiert. Da Komponenten selbst rendern nach der Ausführung von Code im Ereignishandler, werden die Updates der Eigenschaft in der Regel sofort in der Benutzeroberfläche dargestellt.

Mithilfe von `bind` mit einem `CurrentValue` Eigenschaft (`<input bind="@CurrentValue">`) entspricht im Wesentlichen die folgenden:

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

Wenn die Komponente gerendert wird, die `value` des Eingabeelements stammt aus der `CurrentValue` Eigenschaft. Wenn der Benutzer, in das Textfeld eingibt der `onchange` Ereignis wird ausgelöst, und die `CurrentValue` -Eigenschaft auf den geänderten Wert festgelegt ist. In Wirklichkeit die codegenerierung ist ein wenig komplexer, da `bind` behandelt einige Fälle, in denen typkonvertierungen werden ausgeführt. Im Prinzip `bind` ordnet den aktuellen Wert eines Ausdrucks mit einem `value` -Attribut und die Handles-Änderungen, die mit den registrierten Handler.

Zusätzlich zu `onchange`, die Eigenschaft kann mit anderen Ereignissen wie gebunden werden `oninput` als expliziter darüber, was zum Binden an:

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

Im Gegensatz zu `onchange`, `oninput` ausgelöst wird, für jedes Zeichen, die in das Textfeld eingegeben wird.

**Formatzeichenfolgen**

Die Datenbindung funktioniert mit <xref:System.DateTime> Formatzeichenfolgen. Andere Formatausdrücke, z. B. Währungsdaten oder Zahlenformate, sind zu diesem Zeitpunkt nicht verfügbar.

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Die `format-value` Attribut gibt an, das Datumsformat zuweisen der `value` von der `input` Element. Das Format wird auch beim Analysieren des Werts verwendet bei der ein `onchange` Ereignis auftritt.

**Komponentenparameter**

Bindung erkennt außerdem die Parameter der Komponente, wobei `bind-{property}` können einen Eigenschaftswert für Komponenten binden.

Die folgende Komponente verwendet `ChildComponent` und bindet die `ParentYear` Parameter aus dem übergeordneten Element der `Year` Parameter in der untergeordneten Komponente:

Übergeordnete Komponente:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Untergeordnete Komponente:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

Beim Laden der `ParentComponent` erzeugt das folgende Markup:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Wenn der Wert des der `ParentYear` -Eigenschaft geändert wird, durch Auswählen der Schaltfläche in der `ParentComponent`, wird die `Year` Eigenschaft der `ChildComponent` wird aktualisiert. Der neue Wert des `Year` in der Benutzeroberfläche gerendert wird bei der `ParentComponent` gerendert wird:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Die `Year` Parameter bindbar ist, da sie eine begleitende hat `YearChanged` -Ereignis, das den Typ des entspricht der `Year` Parameter.

Gemäß der Konvention `<ChildComponent bind-Year="@ParentYear" />` ist im Wesentlichen äquivalent zum Schreiben von Code,

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

Im Allgemeinen für die eine Eigenschaft gebunden werden kann, in eine entsprechende Event Handler `bind-property-event` Attribut.

## <a name="event-handling"></a>Ereignisbehandlung

Razor-Komponenten bieten Funktionen für die Ereignisbehandlung. Für ein HTML-Element-Attribut mit dem Namen `on<event>` (z. B. `onclick`, `onsubmit`) mit einem Wert für typisierte Delegaten-Razor-Komponenten, der Wert des Attributs als Ereignishandler behandelt. Den Namen des Attributs beginnt immer mit `on`.

Der folgende code Ruft die `UpdateHeading` Methode, wenn die Schaltfläche in der Benutzeroberfläche ausgewählt wird:

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Der folgende code Ruft die `CheckboxChanged` Methode, wenn Sie das Kontrollkästchen in der Benutzeroberfläche geändert wird:

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

-Ereignishandler können auch sein, asynchrone und Zurückgeben einer <xref:System.Threading.Tasks.Task>. Besteht keine Notwendigkeit, manuell aufzurufen `StateHasChanged()`. Ausnahmen werden protokolliert, wenn sie auftreten.

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Für einige Ereignisse sind ereignisspezifische Ereignisargumenttypen zulässig. Wenn Sie den Zugriff auf diese Art von Ereignis nicht erforderlich ist, muss es nicht im Aufruf Methode.

Die Liste der unterstützten Ereignisargumente ist:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

Lambda-Ausdrücke können auch verwendet werden:

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Häufig ist es sinnvoll, um über zusätzlichen Werten, z. B. schließen, wenn eine Gruppe von Elementen zu durchlaufen. Das folgende Beispiel erstellt drei Schaltflächen, von denen jedes Aufrufen `UpdateHeading` ein Ereignisargument übergeben (`UIMouseEventArgs`) und die Schaltfläche "-Nummer (`buttonNumber`) Wenn auf der Benutzeroberfläche ausgewählt:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Führen Sie **nicht** verwenden die Schleifenvariable (`i`) in einem `for` Schleife direkt in einem Lambda-Ausdruck. Andernfalls wird dieselbe Variable verwendet, von allen Lambdaausdrücken verursacht `i`Wert, der in alle Lambdas identisch sein. Immer der Wert in einer lokalen Variable erfasst (`buttonNumber` im vorherigen Beispiel) und anschließend.

## <a name="capture-references-to-components"></a>Erfassen Sie Verweise auf Komponenten

Komponentenverweise bieten einem Möglichkeit Get einen Verweis auf eine Instanz der Komponente, damit Sie Befehle an diese Instanz ist, z. B. ausgeben können `Show` oder `Reset`. Wenn einen Komponentenverweis erfassen möchten, fügen einen `ref` -Attribut auf den untergeordneten-Komponente und anschließend definieren Sie ein Feld mit dem gleichen Namen und den gleichen Typ wie die untergeordneten Komponente.

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Wenn die Komponente gerendert wird, die `loginDialog` Feld wird aufgefüllt, mit der `MyLoginDialog` untergeordneten Komponenteninstanz. Sie können dann .NET Methoden auf die Instanz der Komponente aufrufen.

> [!IMPORTANT]
> Die `loginDialog` Variable wird nur aufgefüllt, nachdem die Komponente gerendert wird und die Ausgabe enthält die `MyLoginDialog` Element. Bis zu diesem Zeitpunkt ist es nichts zu verweisen. Verwenden, um Komponenten Verweise zu bearbeiten, nachdem die Komponente vollständig gerendert wurde die `OnAfterRenderAsync` oder `OnAfterRender` Methoden.

Während eine ähnliche Syntax zum Erfassen von komponentenverweisen verwendet werden. [erfassen Elementverweise](xref:razor-components/javascript-interop#capture-references-to-elements), ist es keine [JavaScript-Interop](xref:razor-components/javascript-interop) Feature. Komponentenverweise sind nicht für JavaScript-Code übergeben. Sie werden nur in .NET-Code verwendet.

> [!NOTE]
> Führen Sie **nicht** Komponente verweist, geändert wird, den Status der untergeordneten Komponenten verwenden. Verwenden Sie stattdessen normalen deklarative Parameter, um Daten an den untergeordneten Komponenten übergeben. Dies bewirkt, dass untergeordneten Komponenten, die zum korrekten Zeitpunkt automatisch erneut rendern.

## <a name="lifecycle-methods"></a>Lebenszyklusmethoden

`OnInitAsync` und `OnInit` Ausführen von Code zum Initialisieren der Komponente. Verwenden Sie zum Ausführen eines asynchronen Vorgangs `OnInitAsync` und `await` Schlüsselwort für den Vorgang:

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

Verwenden Sie für einen synchronen Vorgang `OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` und `OnParametersSet` werden aufgerufen, wenn eine Komponente Parameter, aus dem übergeordneten Element empfangen hat und die Werte an Eigenschaften zugewiesen werden. Diese Methoden werden nach der komponenteninitialisierung ausgeführt, und klicken Sie dann jedes Mal die Komponente wird gerendert:

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

`OnAfterRenderAsync` und `OnAfterRender` werden aufgerufen, nachdem eine Komponente vollständig gerendert wurde. Datenelement und Verweise werden an diesem Punkt aufgefüllt. Verwenden Sie diese Phase, um zusätzliche Initialisierung Schritte mit den gerenderten Inhalt, z. B. das Aktivieren von JavaScript-Bibliotheken von Drittanbietern, die für die gerenderte DOM-Elemente verwendet werden.

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` kann überschrieben werden, um Code auszuführen, bevor die Parameter festgelegt werden:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Wenn `base.SetParameters` wird nicht aufgerufen, der benutzerdefinierte Code kann zu interpretieren eingehende Parameter in einer Weise, die erforderlich sind. Die eingehende Parameter sind beispielsweise nicht erforderlich, um die Eigenschaften der Klasse zugewiesen werden.

`ShouldRender` kann überschrieben werden, um die Aktualisierung der Benutzeroberfläche zu unterdrücken. Wenn die Implementierung gibt `true`, die Benutzeroberfläche aktualisiert wird. Auch wenn `ShouldRender` wird außer Kraft gesetzt, wird immer zuerst die Komponente gerendert.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Freigabe von Komponenten mit "IDisposable"

Wenn eine Komponente implementiert <xref:System.IDisposable>, [Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose) wird aufgerufen, wenn die Komponente, die von der Benutzeroberfläche entfernt wurde. Die folgende Komponente verwendet `@implements IDisposable` und `Dispose` Methode:

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Routing

Routing in der Razor-Komponenten erfolgt durch die Bereitstellung einer routenvorlage jeder Komponente zugegriffen werden kann, in der app.

Wenn eine *cshtml* -Datei mit ein `@page` Richtlinie wird kompiliert, wird die generierte Klasse wird eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> die routenvorlage angeben. Zur Laufzeit sucht der Router Komponentenklassen mit einem `RouteAttribute` und rendert, welche Komponente eine routenvorlage verfügt, die mit der angeforderten URL übereinstimmt.

Mehrere routenvorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anfragen für `/BlazorRoute` und `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Routenparameter

Komponenten können Routenparameter erhalten, von der routenvorlage angegeben werden, der `@page` Richtlinie. Der Router verwendet Routenparameter, um die entsprechende Komponentenparameter aufzufüllen.

*RouteParameter.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

Optionale Parameter werden nicht unterstützt, daher werden zwei `@page` Anweisungen sind im obigen Beispiel angewendet. Die erste ermöglicht die Navigation zu der Komponente ohne Parameter. Die zweite `@page` Direktive nimmt die `{text}` Routenparameter und weist den Wert der `Text` Eigenschaft.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Vererbung der Basisklasse für eine Umgebung "Code-Behind"

Komponentendateien (*.cshtml*) Mischen von HTML-Markup und C# Verarbeitung von Code in der gleichen Datei. Die `@inherits` Richtlinie kann verwendet werden, um apps mit Razor-Komponenten "Code-Behind" nutzen können, die Komponente Markup von Verarbeitungscode trennt.

Die [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) zeigt, wie eine Komponente eine Basisklasse erben kann `BlazorRocksBase`, um die Eigenschaften und Methoden der Komponente bereitzustellen.

*BlazorRocks.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Die Basisklasse ableiten sollten `ComponentBase`.

## <a name="razor-support"></a>Razor-Unterstützung

**Razor-Anweisungen**

Razor-Anweisungen werden in der folgenden Tabelle angezeigt.

| Direktive | Beschreibung |
| --------- | ----------- |
| [\@Funktionen](xref:mvc/views/razor#section-5) | Fügt eine C# Codeblock, um eine Komponente. |
| `@implements` | Implementiert eine Schnittstelle für die generierte Component-Klasse. |
| [\@Erbt](xref:mvc/views/razor#section-3) | Bietet vollständige Kontrolle über die Klasse, die Komponente erbt. |
| [\@inject](xref:mvc/views/razor#section-4) | Ermöglicht service Injection aus der [Dienstcontainer](xref:fundamentals/dependency-injection). Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection). |
| `@layout` | Gibt eine Layoutkomponente. Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden. |
| [\@Seite "](xref:razor-pages/index#razor-pages) | Gibt an, dass die Komponente Anforderungen direkt verarbeiten soll. Die `@page` Richtlinie kann mit einer Route und optionale Parameter angegeben werden. Im Gegensatz zu Razor Pages die `@page` Richtlinie muss nicht die erste Anweisung am Anfang der Datei sein. Weitere Informationen finden Sie unter [Routing](xref:razor-components/routing). |
| [\@Mithilfe von](xref:mvc/views/razor#using) | Fügt der C# `using` Anweisung der generierten Component-Klasse. |
| [\@addTagHelper](xref:mvc/views/razor#tag-helpers) | Verwenden Sie `@addTagHelper` Komponente in einer anderen Assembly als die Assembly der app verwendet. |

**Bedingte Attribute**

Attribute werden bedingt gerendert basierend auf dem .NET-Wert. Wenn der Wert ist `false` oder `null`, das Attribut nicht gerendert. Wenn der Wert ist `true`, das Attribut gerendert wird minimiert.

Im folgenden Beispiel `IsCompleted` bestimmt, ob `checked` wird in das Markup des Steuerelements gerendert:

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

Wenn `IsCompleted` ist `true`, als Sie das Kontrollkästchen gerendert wird:

```html
<input type="checkbox" checked>
```

Wenn `IsCompleted` ist `false`, als Sie das Kontrollkästchen gerendert wird:

```html
<input type="checkbox">
```

**Weitere Informationen zu Razor**

Weitere Informationen zu Razor, finden Sie unter den [Razor-syntaxverweis](xref:mvc/views/razor).

## <a name="raw-html"></a>Unformatiertes HTML

Zeichenfolgen werden normal gerendert mithilfe von DOM-Knoten, was bedeutet, dass alle darin enthaltenen möglicherweise Markup wird ignoriert und als Literaltext behandelt. Um unformatiertes HTML zu rendern, umschließen Sie den HTML-Inhalt in einem `MarkupString` Wert. Der Wert wird als HTML- oder SVG analysiert und in das DOM eingefügt

> [!WARNING]
> Rendern von unformatierten HTML-erstellt aus jeder von nicht vertrauenswürdigen Quelle ist eine **Sicherheitsrisiko** und sollte vermieden werden.

Das folgende Beispiel zeigt die Verwendung der `MarkupString` Typ um einen Block mit statischem HTML-Inhalt in die gerenderte Ausgabe einer Komponente hinzuzufügen:

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Auf Vorlagen basierende Komponenten

Auf Vorlagen basierende Komponenten sind Komponenten, die eine oder mehrere UI-Vorlagen als Parameter akzeptieren, die dann als Teil der Renderinglogik der Komponente verwendet werden können. Auf Vorlagen basierende Komponenten können Sie Komponenten höherer Ebene zu erstellen, die als reguläre Komponenten besser wiederverwendbar sind. Einige Beispiele enthalten:

* Eine Tabelle-Komponente, die einem Benutzer ermöglicht, Vorlagen für Header, Zeilen und Fußzeile der Tabelle angeben.
* Eine Listenkomponente, die einem Benutzer ermöglicht, eine Vorlage zum Rendern von Elementen in einer Liste angeben.

### <a name="template-parameters"></a>Vorlagenparameter

Eine auf Vorlagen basierende Komponente wird definiert, indem Sie eine oder mehrere Parameter für die Komponente vom Typ `RenderFragment` oder `RenderFragment<T>`. Ein Rendering-Fragment stellt ein Segment der Benutzeroberfläche, die von der Komponente gerendert wird. Ein Fragment gerendert hat optional einen Parameter, der aufgerufen wird, der Rendering-Fragment angegeben werden kann.

*Components/TableTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

Wenn Sie eine auf Vorlagen basierende Komponente zu verwenden, die Parameter der Vorlage können angegeben werden von untergeordneten Elementen, die den Namen der Parameter übereinstimmen (`TableHeader` und `RowTemplate` im folgenden Beispiel):

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a>Kontext-Vorlagenparameter

Komponente-Argumenten des Typs `RenderFragment<T>` übergeben, da Elemente einen impliziten Parameter mit dem Namen `context` (z. B. aus dem vorherigen Codebeispiel `@context.PetId`), aber Sie können ändern, den Parameter mit der `Context` Attribut für das untergeordnete Element Element. Im folgenden Beispiel die `RowTemplate` des Elements `Context` -Attribut gibt an, die `pet` Parameter:

```cshtml
<TableTemplate Items="@pets">
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

Alternativ können Sie angeben der `Context` Attributs im Komponentenelement. Das angegebene `Context` Attribut gilt für alle Parameter der angegebenen Vorlage. Dies kann nützlich sein, wenn der Inhalt Parametername für implizite untergeordneten Inhalt angeben möchten (nur mit jedem Zeilenumbruch untergeordnetes Element). Im folgenden Beispiel die `Context` Attribut angezeigt wird, auf die `TableTemplate` Element und gilt für alle Parameter der Vorlage:

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a>Typisierte generische Komponenten

Auf Vorlagen basierende Komponenten sind häufig generisch typisiert. Beispielsweise kann eine generische Liste Ansichtsvorlage-Komponente zum Rendern verwendet `IEnumerable<T>` Werte. Verwenden Sie zum Definieren einer generischen Komponente der `@typeparam` Direktive, um die Typparameter angeben.

*Components/ListViewTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

Wenn Sie typisierte generische Komponenten zu verwenden, wird der Typparameter möglichst abgeleitet:

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Andernfalls muss der Typparameter explizit angegeben werden unter Verwendung eines Attributs, das mit dem Namen des Typparameters übereinstimmt. Im folgenden Beispiel `TItem="Pet"` gibt den Typ an:

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Kaskadierende Rückgabewerte und Parameter

In einigen Szenarien ist es unpraktisch für den Datenfluss aus einer übergeordneten Komponente in eine untergeordnete Komponente [Parameter der Komponente](#component-parameters), besonders wenn es mehrere Ebenen der Komponente. Lösen dieses Problem durch die Bereitstellung einer praktische Methode für eine Vorgänger-Komponente, um einen Wert für alle seine untergeordneten Komponenten bereitzustellen, Cascading Rückgabewerte und Parameter. Kaskadierende Rückgabewerte und Parameter bieten auch einen Ansatz für Komponenten zu koordinieren.

### <a name="theme-example"></a>Design-Beispiel

In der folgenden *Design* Beispiel aus der Beispiel-app die `ThemeInfo` Klasse gibt an, das Designinformationen zur Übertragung in der Komponentenhierarchie, sodass alle Schaltflächen innerhalb eines bestimmten Teils der app im gleichen Stil freigeben.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Eine Vorgänger-Komponente kann es sich um einen kaskadierende Wert mithilfe der Komponente der kaskadierende Wert angeben. Die Komponente der kaskadierende Wert dient als Wrapper für eine Unterstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur.

Die Beispiel-app gibt z. B. Designinformationen (`ThemeInfo`) in einem von der app-Layouts als kaskadierenden Parameter für alle Komponenten, aus denen der Layout-Text, der die `@Body` Eigenschaft. `ButtonClass` einen Wert zugewiesen `btn-success` in der Layoutkomponente. Eine untergeordnete Komponente kann diese Eigenschaft durch Nutzen der `ThemeInfo` cascading-Objekt.

*Shared/CascadingValuesParametersLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Vornehmen von kaskadierenden Werten verwenden, die Komponenten deklarieren kaskadierende Parameter mithilfe der `[CascadingParameter]` Attribut oder basierend auf einem Zeichenfolge-Name-Wert:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

Bindung mit einem Namen Zeichenfolgenwert ist relevant, wenn Sie mehrere kaskadierende Werte vom gleichen Typ aufweisen und innerhalb der gleichen Teilstruktur unterscheiden müssen.

Kaskadierender Werte werden an kaskadierende Parameter vom Typ gebunden.

In der Beispiel-app, bindet die kaskadierenden Werten Parameter Design-Komponente an die `ThemeInfo` kaskadierende Wert, der einem kaskadierenden Parameter. Der Parameter wird verwendet, die CSS-Klasse für eine der Schaltflächen angezeigt, die von der Komponente festlegen.

*Pages/CascadingValuesParametersTheme.cshtml*:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a>TabSet-Beispiel

Kaskadierende Parameter ermöglichen auch die Komponenten für die Zusammenarbeit in der Komponentenhierarchie. Betrachten Sie beispielsweise die folgenden *TabSet* Beispiel in der Beispiel-app.

Die Beispielapp verfügt über eine `ITab` -Schnittstelle, die Registerkarten implementieren:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Die kaskadierenden Werten Parameter TabSet-Komponente verwendet die legen Sie die Registerkarte "-Komponente, die mehrere Registerkarte Komponenten enthält:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

Der untergeordneten Registerkarte Komponenten werden nicht explizit als Parameter übergeben, auf der Registerkarte "festgelegt. Stattdessen werden die Komponenten der untergeordneten Registerkarte Teil des untergeordneten Inhalts des legen Sie die Registerkarte. Allerdings muss legen Sie die Registerkarte noch jede Registerkarte Komponente kennen, damit er die Header und der aktiven Registerkarte rendern kann. Zu diesem Zweck zu aktivieren, ohne zusätzlichen Code, der Komponente legen Sie die Registerkarte *bieten sich selbst als kaskadierenden Wert* , wird dann von der untergeordneten Registerkarte Komponenten übernommen.

*Components/TabSet.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

Die Erfassung der untergeordneten Registerkarte Komponenten festlegen als kaskadierenden Parameter, die mit Registerkarte die Registerkarte "-Komponenten selbst auf die Registerkarte" festgelegt und die Koordinate hinzufügen auf der Registerkarte ist aktiv.

*Components/Tab.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a>Razor-Vorlagen

Rendern von Fragmenten können mithilfe der Syntax für Razor-Vorlage definiert werden. Razor-Vorlagen sind eine Möglichkeit, einen UI-Codeausschnitt definiert und das folgende Format angenommen:

```cshtml
@<tag>...</tag>
```

Im folgende Beispiel wird veranschaulicht, wie an `RenderFragment` und `RenderFragment<T>` Werte.

*RazorTemplates.cshtml*:

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

Rendern Sie Fragmente mit Razor-Vorlagen, die auf Vorlagen basierende Komponenten als Argumente übergeben oder direkt gerendert werden können, definiert. Beispielsweise werden die vorherigen Vorlagen direkt mit dem folgenden Razor-Markup gerendert:

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

Gerenderte Ausgabe:

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a>Manuelle RenderTreeBuilder Logik

`Microsoft.AspNetCore.Components.RenderTree` Stellt Methoden zum Bearbeiten von Komponenten und Elemente, einschließlich Erstellen von Komponenten manuell in C# Code.

> [!NOTE]
> Verwenden von `RenderTreeBuilder` zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine fehlerhafte Komponente (z. B. eine nicht geschlossene Markuptag) kann zu nicht definiertem Verhalten führen.

Betrachten Sie die folgende Pet-Details-Komponente (*PetDetails.razor* in Razor-Komponenten; *PetDetails.cshtml* in Blazor), die manuell in eine andere Komponente erstellt werden kann:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel der Schleife in der `CreateComponent` Methode generiert drei Pet-Details-Komponenten. Beim Aufrufen von `RenderTreeBuilder` Methoden zum Erstellen der Komponenten (`OpenComponent` und `AddAttribute`), sind die Sequenznummern Source Code Zeilennummern. Die Razor-Komponenten, die die Sequenznummern, die für unterschiedliche Codezeilen nicht unterschiedliche Unterschied-Algorithmus basiert aufrufen aufrufen. Beim Erstellen einer Komponente mit `RenderTreeBuilder` Methoden, die hartcodiert die Argumente für Sequenznummern. **Verwenden eine Berechnung oder einen Indikator, um die Sequenznummer zu generieren, kann zu Leistungseinbußen führen.**

Erstellt die Komponente (*BuiltContent.razor* in Razor-Komponenten; *BuiltContent.cshtml* in Blazor):

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
