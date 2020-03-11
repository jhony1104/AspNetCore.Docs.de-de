---
title: Razor-Syntaxreferenz für ASP.NET Core
author: rick-anderson
description: Informationen zur Razor-Markupsyntax zum Einbetten von serverbasiertem Code in Webseiten
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: e9d2e42ba3c36bc1661739f3b105ec8efe03de48
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651727"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor-Syntaxreferenz für ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)und [Dan vicarel](https://github.com/Rabadash8820)

Razor stellt eine Markupsyntax zum Einbetten von serverbasiertem Code in Webseiten dar. Die Razor-Syntax besteht aus dem Razor-Markup, C# und HTML. Dateien, die Razor enthalten, besitzen in der Regel die Dateierweiterung *.cshtml*. Razor ist auch in [Razor-Komponentendateien](xref:blazor/components) ( *.razor*) enthalten.

## <a name="rendering-html"></a>Rendern von HTML

Die Standardsprache für Razor ist HTML. Das Rendern von HTML-Code aus einem Razor-Markup funktioniert genauso wie das Rendern von HTML aus einer HTML-Datei. Ein HTML-Markup wird in Razor-Dateien mit der Erweiterung *.cshtml* vom Server unverändert gerendert.

## <a name="razor-syntax"></a>Razor-Syntax

Razor unterstützt C# und verwendet für den Übergang von HTML zu C# das `@`-Symbol. Razor wertet C#-Ausdrücke aus und rendert sie in der HTML-Ausgabe.

Folgt auf ein `@`-Symbol [ein für Razor reserviertes Schlüsselwort](#razor-reserved-keywords), wechselt es in ein Razor-spezifisches Markup. Andernfalls erfolgt der Übergang in normales C#.

Verwenden Sie ein zweites `@`-Symbol, um im Razor-Markup ein `@`-Symbol mit Escapezeichen zu versehen:

```cshtml
<p>@@Username</p>
```

Der Code wird in HTML mit einem einzelnen `@`-Symbol gerendert:

```html
<p>@Username</p>
```

Bei HTML-Attributen und Inhalt mit E-Mail-Adressen wird das `@`-Symbol nicht als ein Übergangszeichen behandelt. Die E-Mail-Adressen im folgenden Beispiel bleiben von der Razor-Analyse unverändert:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implizite Razor-Ausdrücke

Implizite Razor-Ausdrücke beginnen mit `@` gefolgt von C#-Code:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Mit Ausnahme des C#-Schlüsselworts `await` dürfen implizite Ausdrücke keine Leerzeichen enthalten. Wird die C#-Anweisung eindeutig beendet, können auch Leerzeichen verwendet werden:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implizite Ausdrücke dürfen **keine** C#-Generics enthalten, da die Zeichen innerhalb der Klammern (`<>`) als HTML-Tag interpretiert werden. Der folgende Code ist **ungültig**:

```cshtml
<p>@GenericMethod<int>()</p>
```

Der vorhergehende Code erzeugt einen Compilerfehler, der folgendermaßen aussehen kann:

* Das Element „int“ wurde nicht geschlossen. Alle Elemente müssen entweder selbstschließend sein oder ein zugehöriges Endtag besitzen.
* Die Methodengruppe „GenericMethod“ kann nicht in den Nichtdelegattyp „Objekt“ konvertiert werden. Wollten Sie die Methode aufrufen?

Generische Methodenaufrufe müssen von einem [expliziten Razor-Ausdruck](#explicit-razor-expressions) oder einem [Razor-Codeblock](#razor-code-blocks) umschlossen werden.

## <a name="explicit-razor-expressions"></a>Explizite Razor-Ausdrücke

Explizite Razor-Ausdrücke bestehen aus einem `@`-Symbol mit ausgeglichener Klammer. Zum Rendern der Uhrzeit von letzter Woche wird folgendes Razor-Markup verwendet:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Jeglicher Inhalt innerhalb der `@()`-Klammer wird ausgewertet und in der Ausgabe gerendert.

Die im vorherigen Abschnitt beschriebenen impliziten Ausdrücke dürfen grundsätzlich keine Leerzeichen enthalten. Im folgenden Code wird eine Woche nicht von der aktuellen Uhrzeit abgezogen:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Der Code rendert den folgenden HTML-Code:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explizite Ausdrücke können zum Verketten von Text mit einem Ergebnis des Ausdrucks verwendet werden:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Ohne den expliziten Ausdruck wird `<p>Age@joe.Age</p>` als E-Mail-Adresse behandelt und `<p>Age@joe.Age</p>` gerendert. `<p>Age33</p>` wird gerendert, wenn es als expliziter Ausdruck geschrieben wird.

Explizite Ausdrücke können zum Rendern der Ausgabe von generischen Methoden in *.cshtml*-Dateien verwendet werden. Das folgende Markup zeigt, wie der weiter oben gezeigte Fehler behoben wird, der durch die Klammern von C#-Generika verursacht wurde. Der Code wird als expliziter Ausdruck geschrieben:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Codieren von Ausdrücken

C#-Ausdrücke, die als Zeichenfolge ausgewertet werden, werden HTML-codiert. C#-Ausdrücke, die als `IHtmlContent` ausgewertet werden, werden direkt durch `IHtmlContent.WriteTo` gerendert. C#-Ausdrücke, die nicht als `IHtmlContent` ausgewertet werden, werden durch `ToString` in eine Zeichenfolge konvertiert und vor dem Rendern codiert.

```cshtml
@("<span>Hello World</span>")
```

Der Code rendert den folgenden HTML-Code:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Der HTML-Code wird im Browser folgendermaßen angezeigt:

```html
<span>Hello World</span>
```

Die Ausgabe `HtmlHelper.Raw` wird nicht codiert, sondern als HTML-Markup gerendert.

> [!WARNING]
> Die Verwendung von `HtmlHelper.Raw` bei einer nicht bereinigten Benutzereingabe stellt ein Sicherheitsrisiko dar. Benutzereingaben können schädlichen JavaScript-Code oder andere Sicherheitsrisiken enthalten. Das Bereinigen von Benutzereingaben ist schwierig. Vermeiden Sie daher die Verwendung von `HtmlHelper.Raw` bei Benutzereingaben.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Der Code rendert den folgenden HTML-Code:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor-Codeblöcke

Razor-Codeblöcke beginnen mit `@` und sind von `{}` eingeschlossen. Im Gegensatz zu Ausdrücken wird C#-Code in Codeblöcken nicht gerendert. Codeblöcke und Ausdrücke in einer Ansicht nutzen den gleichen Bereich und werden der Reihe nach definiert:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Der Code rendert den folgenden HTML-Code:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Deklarieren Sie in Codeblöcken [lokale Funktionen](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) mit Markup als Vorlagenmethoden:

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

Der Code rendert den folgenden HTML-Code:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Implizite Übergänge

Die Standardsprache in einem Codeblock ist C#. Die Razor Page kann jedoch zurück zu HTML wechseln:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Durch Trennzeichen getrennte explizite Übergänge

Soll ein Unterabschnitt eines Codeblocks in HTML gerendert werden, umschließen Sie die Zeichen, die gerendert werden sollen, mit dem Razor-Tag `<text>`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Verwenden Sie diese Methode zum Rendern von HTML-Code, der nicht von einem HTML-Tag umschlossen ist. Ohne HTML- oder Razor-Tag tritt ein Razor-Laufzeitfehler auf.

Das `<text>`-Tag ist nützlich, wenn Sie beim Rendern von Inhalt Leerzeichen steuern möchten:

* Nur der Inhalt zwischen den `<text>`-Tags wird gerendert.
* In der HTML-Ausgabe werden keine Leerzeichen vor oder nach dem `<text>`-Tag angezeigt.

### <a name="explicit-line-transition"></a>Explizite Zeilenübergänge

Verwenden Sie die `@:`-Syntax, um den Rest einer kompletten Zeile als HTML-Code in einem Codeblock zu rendern:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Ohne das `@:`-Symbol im Code wird ein Razor-Laufzeitfehler erzeugt.

Zusätzliche `@`-Zeichen in einer Razor-Datei können zu Compilerfehlern bei späteren Anweisungen im Block führen. Diese Compilerfehler können dann schwer nachvollziehbar sein, da der tatsächliche Fehler vor dem gemeldeten Fehler auftritt. Dieser Fehler tritt häufig auf, wenn mehrere implizite/explizite Ausdrücke in einem einzigen Codeblock kombiniert werden.

## <a name="control-structures"></a>Steuerungsstrukturen

Steuerungsstrukturen sind eine Erweiterung von Codeblöcken. Alle Aspekte von Codeblöcken (Übergang zu Markup, Inline-C#) gelten auch für die folgenden Strukturen:

### <a name="conditionals-if-else-if-else-and-switch"></a>Die Bedingungen \@if, else if, else und \@switch

`@if` steuert, wann der Code ausgeführt wird:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` und `else if` erfordern kein `@`-Symbol:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Im folgenden Markup wird die Verwendung einer switch-Anweisung veranschaulicht:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Schleifen \@for, \@foreach, \@while und \@do while

Auf Vorlagen basierender HTML-Code kann mit Anweisungen zur Steuerung von Schleifen gerendert werden. So rendern Sie eine Liste mit Personen.

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Die folgenden Schleifenanweisungen werden unterstützt:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Verbund \@using

In C# kann mit einer `using`-Anweisung sichergestellt werden, dass ein Objekt freigegeben wurde. In Razor wird derselbe Mechanismus verwendet, um HTML-Hilfsprogramme zu erstellen, die zusätzliche Inhalte enthalten. Im folgenden Code wird ein `<form>`-Tag mit der `@using`-Anweisung durch HTML-Hilfsprogramme gerendert:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@try, catch, finally

Die Behandlung von Ausnahmen ist vergleichbar mit C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@lock

Razor kann kritische Abschnitte mit lock-Anweisungen schützen:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Kommentare

Razor unterstützt C#- und HTML-Kommentare:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Der Code rendert den folgenden HTML-Code:

```html
<!-- HTML comment -->
```

Razor-Kommentare werden vom Server entfernt, bevor die Webseite gerendert wird. Zur Abgrenzung der Kommentare verwendet Razor `@*  *@`. Der folgende Code ist auskommentiert, damit vom Server kein Markup gerendert wird:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Anweisungen

Razor-Anweisungen werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem `@`-Symbol dargestellt. Eine Anweisung ändert in der Regel die Analyse einer Ansicht oder aktiviert unterschiedliche Funktionen.

Wenn Sie wissen, wie von Razor Code für eine Ansicht generiert wird, erleichtert dies das Verständnis dafür, wie Anweisungen funktionieren.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Durch den Code wird eine Klasse ähnlich der folgenden Klasse generiert:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Im Abschnitt [Überprüfen der Razor-C#-Klasse, die für eine Ansicht generiert wurde](#inspect-the-razor-c-class-generated-for-a-view) weiter unten wird erklärt, wie die generierte Klasse angezeigt wird.

### <a name="attribute"></a>\@attribute

Die `@attribute`-Anweisung fügt das angegebene Attribut zu der Klasse der generierten Seite oder Ansicht hinzu. Im folgenden Beispiel wird das `[Authorize]`-Attribut hinzugefügt:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@code

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Der `@code`-Block ermöglicht einer [Razor-Komponente](xref:blazor/components) das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zu einer Komponente:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Für Razor-Komponenten ist `@code` ein Alias von [`@functions`](#functions) und wird über `@functions` empfohlen. Mehrere `@code`-Blöcke sind zulässig.

::: moniker-end

### <a name="functions"></a>\@functions

Die `@functions`-Anweisung ermöglicht das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zur generierten Klasse:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

Verwenden Sie in [Razor-Komponenten](xref:blazor/components)`@code` über `@functions`, um C#-Member hinzuzufügen.

::: moniker-end

Beispiel:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Der Code generiert das folgende HTML-Markup:

```html
<div>From method: Hello</div>
```

Der folgende Code wird in der Razor-C#-Klasse generiert:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`-Methoden dienen als Vorlagenmethoden, wenn sie Markup aufweisen:

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

Der Code rendert den folgenden HTML-Code:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@implements

Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.

Im folgenden Beispiel wird <xref:System.IDisposable?displayProperty=fullName> implementiert, sodass die <xref:System.IDisposable.Dispose*>-Methode aufgerufen werden kann:

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a>\@inherits

Die `@inherits`-Anweisung bietet uneingeschränkten Zugriff auf die Klasse, die die Ansicht erbt:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Der folgende Code ist ein benutzerdefinierter Typ der Razor Page:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` wird in einer Ansicht angezeigt:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Der Code rendert den folgenden HTML-Code:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` und `@inherits` können in derselben Ansicht verwendet werden. `@inherits` kann in einer *_ViewImports.cshtml*-Datei verwendet werden, die von der Ansicht importiert wird:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Der folgende Code ist ein Beispiel für eine stark typisierte Ansicht:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Wird „rick@contoso.com“ im Modell übergeben, generiert die Ansicht das folgende HTML-Markup:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@inject

Mit der `@inject`-Anweisung kann die Razor Page einen Dienst aus dem [Dienstcontainer](xref:fundamentals/dependency-injection) in eine Ansicht einfügen. Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@layout

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Die `@layout`-Anweisung gibt ein Layout für eine Razor-Komponente an. Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden. Weitere Informationen finden Sie unter <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@model

*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*

Die `@model`-Anweisung gibt den Typ des Modells an, das an eine Ansicht oder Seite übergeben wird:

```cshtml
@model TypeNameOfModel
```

In einer mit einzelnen Benutzerkonten erstellten ASP.NET Core MVC- oder Razor Pages-App enthält *Views/Account/Login.cshtml* die folgende Modelldeklaration:

```cshtml
@model LoginViewModel
```

Die generierte Klasse erbt von `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor macht eine `Model`-Eigenschaft für den Zugriff auf das an die Ansicht übergebene Modell verfügbar:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Die `@model`-Anweisung gibt den Typ der `Model`-Eigenschaft an. Die Anweisung gibt das `T` in `RazorPage<T>` der generierten Klasse an, von der die Ansicht abgeleitet wird. Wird die `@model`-Anweisung nicht angegeben, hat die `Model`-Eigenschaft den Typ `dynamic`. Weitere Informationen finden Sie unter [Stark typisierte Modelle und das Schlüsselwort @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

Die `@namespace`-Anweisung:

* Legt den Namespace der Klasse der generierten Razor-Seite, der MVC-Ansicht oder der Razor-Komponente fest.
* Legt die vom Stamm abgeleiteten Namespaces einer Seiten-, Ansichts- oder Komponentenklasse aus der nächsten Importdatei in der Verzeichnisstruktur fest: *_ViewImports.cshtml*. (Ansichten oder Seiten) oder *_Imports.razor* (Razor-Komponenten).

```cshtml
@namespace Your.Namespace.Here
```

Für das Razor Pages Beispiel in der folgenden Tabelle gilt:

* Jede Seite importiert *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* enthält `@namespace Hello.World`.
* Jede Seite weist `Hello.World` als Stamm ihres Namespace auf.

| Seite                                        | Namespace                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Die vorstehenden Beziehungen gelten für Importdateien, die mit MVC-Ansichten und Razor-Komponenten verwendet werden.

Wenn mehrere Importdateien über eine `@namespace`-Anweisung verfügen, wird die Datei verwendet, die der Seite, der Ansicht oder der Komponente in der Verzeichnisstruktur am nächsten ist, um den Stammnamespace festzulegen.

Wenn der Ordner *EvenMorePages* im Beispiel oben eine Importdatei mit `@namespace Another.Planet` enthält (oder die Datei *Pages/MorePages/EvenMorePages/Page.cshtml*`@namespace Another.Planet` enthält), finden Sie das Ergebnis in der folgenden Tabelle.

| Seite                                        | Namespace               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@page

::: moniker range=">= aspnetcore-3.0"

Die `@page`-Anweisung hat abhängig vom Typ der Datei, in der Sie verwendet wird, unterschiedliche Auswirkungen. Für die Anweisung gilt:

* In einer *CSHTML*-Datei gibt sie an, dass die Datei eine Razor-Seite ist. Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes) und <xref:razor-pages/index>.
* Gibt an, dass eine Razor-Komponente Anforderungen direkt verarbeiten soll. Weitere Informationen finden Sie unter <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Die `@page`-Anweisung in der ersten Zeile einer *CSHTML*-Datei gibt an, dass die Datei eine Razor-Seite ist. Weitere Informationen finden Sie unter <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@section

*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*

Die `@section`-Anweisung wird in Verbindung mit [MVC- und Razor Pages-Layouts](xref:mvc/views/layout) verwendet, damit Ansichten Seiten Inhalte in verschiedenen Teilen der HTML-Seite rendern können. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="using"></a>\@using

Die `@using`-Anweisung fügt die `using`-C#-Anweisung der generierten Ansicht hinzu:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

[ steuert in ](xref:blazor/components)Razor-Komponenten`@using` außerdem, welche Komponenten sich im Gültigkeitsbereich befinden.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Direktivenattribute

### <a name="attributes"></a>\@attributes

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

`@attributes` ermöglicht es einer Komponente, nicht deklarierte Attribute zu rendern. Weitere Informationen finden Sie unter <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bind

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Die Datenbindung in-Komponenten wird mit dem `@bind`-Attribut erreicht. Weitere Informationen finden Sie unter <xref:blazor/data-binding>.

### <a name="onevent"></a>\@on{EVENT}

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Razor stellt Ereignisbehandlungsfunktionen für Komponenten bereit. Weitere Informationen finden Sie unter <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@on{EVENT}:preventDefault

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Verhindert die Standardaktion für das Ereignis.

### <a name="oneventstoppropagation"></a>\@on{EVENT}:stopPropagation

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Beendet die Ereignisweitergabe für das Ereignis.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Das Direktivenattribut `@key` bewirkt, dass der Komponentenvergleichsalgorithmus die Erhaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert. Weitere Informationen finden Sie unter <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Komponentenverweise (`@ref`) bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können. Weitere Informationen finden Sie unter <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*

Die `@typeparam`-Anweisung deklariert einen generischen Typparameter für die generierte Komponentenklasse. Weitere Informationen finden Sie unter <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Auf Vorlagen basierende Razor-Delegate

Mit Razor-Vorlagen können Sie einen Ausschnitt der Benutzeroberfläche mit dem folgenden Format festlegen:

```cshtml
@<tag>...</tag>
```

Im folgenden Beispiel wird veranschaulicht, wie ein auf Vorlagen basierender Razor-Delegat als <xref:System.Func%602> angegeben wird. Der Typ [dynamic](/dotnet/csharp/programming-guide/types/using-type-dynamic) wird für den Parameter der Methode angegeben, die der Delegat einkapselt. Ein [object](/dotnet/csharp/language-reference/keywords/object)-Typ wird als Rückgabewert des Delegats angegeben. Die Vorlage wird mit <xref:System.Collections.Generic.List%601> von `Pet` mit einer `Name`-Eigenschaft verwendet.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

Die Vorlage wird mit `pets` in einer `foreach`-Anweisung gerendert:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Gerenderte Ausgabe:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Sie können auch eine Razor-Inlinevorlage als Argument für eine Methode angeben. Im folgenden Beispiel nimmt die `Repeat`-Methode eine Razor-Vorlage entgegen. Die Methode verwendet die Vorlage dann zum Erstellen von HTML-Inhalten mit Wiederholungen von Elementen aus einer Liste:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

Wird die Liste mit Haustieren aus dem vorherigen Beispiel verwendet, wird die `Repeat`-Methode wie folgt aufgerufen:

* <xref:System.Collections.Generic.List%601> von `Pet`
* Anzahl der Wiederholungen für jedes Haustier
* Inlinevorlage zur Verwendung für die Elemente einer unsortierten Liste

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Gerenderte Ausgabe:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Taghilfsprogramme

*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*

Die folgenden drei Anweisungen gehören zu den [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro).

| Direktive | Funktion |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Macht Taghilfsprogramme für eine Ansicht verfügbar. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Entfernt die zuvor aus einer Ansicht hinzugefügten Taghilfsprogramme. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Gibt ein Tagpräfix an, um Unterstützung für Taghilfsprogramme zu aktivieren und ihre Verwendung explizit festzulegen. |

## <a name="razor-reserved-keywords"></a>Für Razor reservierte Schlüsselwörter

### <a name="razor-keywords"></a>Razor-Schlüsselwörter

* page (erfordert ASP.NET Core 2.1 oder höher)
* Namespace
* functions
* inherits
* model
* section
* helper (wird derzeit nicht von ASP.NET Core unterstützt)

Razor-Schlüsselwörter werden mit dem Escapezeichen `@(Razor Keyword)` versehen (z.B. `@(functions)`).

### <a name="c-razor-keywords"></a>Razor-C#-Schlüsselwörter

* case
* do
* default
* for
* foreach
* if
* else
* Sperre
* Schalter
* Versuch
* erfassen
* finally
* Mithilfe von
* während

Razor-C#-Schlüsselwörter werden mit dem doppeltem Escapezeichen `@(@C# Razor Keyword)` versehen (z.B. `@(@case)`). Das erste `@` dient als Escapezeichen für den Razor-Parser. Das zweite `@` dient als Escapezeichen für den C#-Parser.

### <a name="reserved-keywords-not-used-by-razor"></a>Reservierte Schlüsselwörter, die nicht von Razor verwendet werden

* class

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Überprüfen der Razor-C#-Klasse, die für eine Ansicht generiert wurde

::: moniker range=">= aspnetcore-2.1"

Bei .NET Core SDK 2.1 oder höher führt das [Razor SDK](xref:razor-pages/sdk) die Kompilierung von Razor-Dateien durch. Beim Erstellen eines Projekts generiert das Razor SDK das Verzeichnis *obj/<build_konfiguration>/<ziel_framework_bezeichnung>/Razor* im Projektstamm. Die Verzeichnisstruktur im *Razor*-Verzeichnis spiegelt die Verzeichnisstruktur des Projekts.

Beachten Sie die folgende Verzeichnisstruktur in einem Razor Pages-Projekt in ASP.NET Core 2.1 für .NET Core 2.1:

* **Areas/**
  * **Admin/**
    * **Pages/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Pages/**
  * **Shared/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

Wenn Sie das Projekt mit der Konfiguration zum *Debuggen* erstellen, wird folgendes *obj*-Verzeichnis erstellt:

* **obj/**
  * **Debug/**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Areas/**
          * **Admin/**
            * **Pages/**
              * *Index.g.cshtml.cs*
        * **Pages/**
          * **Shared/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Öffnen Sie *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*, um die für *Pages/Index.cshtml* generierte Klasse anzuzeigen.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Fügen Sie dem ASP.NET Core MVC-Projekt die folgende Klasse hinzu:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Überschreiben Sie in `Startup.ConfigureServices` die von MVC hinzugefügte `RazorTemplateEngine`-Klasse mit der `CustomTemplateEngine`-Klasse:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Legen Sie auf der `return csharpDocument;`-Anweisung von `CustomTemplateEngine` einen Haltepunkt fest. Wenn die Ausführung des Programms am Haltepunkt angehalten wird, können Sie den Wert von `generatedCode` anzeigen.

![Ansicht „Text-Schnellansicht“ von „generatedCode“](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Ansicht der Suchvorgänge und Groß-/Kleinschreibung

Die Razor-Ansichtsengine führt für Ansichten Suchvorgänge aus, die die Groß-/Kleinschreibung berücksichtigen. Der tatsächliche Suchvorgang wird jedoch vom zugrunde liegenden Dateisystem bestimmt:

* Dateibasierte Quelle:
  * Bei Betriebssystemen, die Dateisysteme ohne Berücksichtigung von Groß-/Kleinschreibung verwenden (z.B. Windows), wird bei Suchvorgängen nach physischen Dateianbietern die Groß- und Kleinschreibung nicht berücksichtigt. `return View("Test")` liefert beispielsweise Treffer für */Views/Home/Test.cshtml*, */Views/home/test.cshtml* sowie für jede andere Schreibweise.
  * Bei Dateisystemen, die die Groß-/Kleinschreibung berücksichtigen (z.B. Linux, OSX sowie mit `EmbeddedFileProvider`), wird die Groß-/Kleinschreibung auch bei Suchvorgängen berücksichtigt. `return View("Test")` liefert beispielsweise ganz konkret Treffer für */Views/Home/Test.cshtml*.
* Vorkompilierte Ansichten: Ab ASP.NET Core 2.0 wird bei der Suche nach vorkompilierten Ansichten unter allen Betriebssystemen die Groß-/Kleinschreibung nicht berücksichtigt. Das Verhalten ist mit dem des physischen Dateianbieters unter Windows identisch. Unterscheiden sich zwei vorkompilierte Ansichten nur in der Groß-/Kleinschreibung, ist das Ergebnis der Suche nicht deterministisch.

Entwicklern wird empfohlen, sich bei der Groß-/Kleinschreibung von Datei- und Verzeichnisnamen an der Schreibweise folgender Begriffe zu orientieren:

* Bereichs-, Controller- und Aktionsnamen
* Razor Pages

Durch Überprüfung der Groß-/Kleinschreibung wird sichergestellt, dass die entsprechenden Ansichten für die Bereitstellungen unabhängig von dem zugrunde liegenden Dateisystem gefunden werden.
