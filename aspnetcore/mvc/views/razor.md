---
title: Razor-Syntaxreferenz für ASP.NET Core
author: rick-anderson
description: Informationen zur Razor-Markupsyntax zum Einbetten von serverbasiertem Code in Webseiten
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/razor
ms.openlocfilehash: baac0ac38a0781cb9c16689cf3e29526b602d8da
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944251"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="67a6f-103">Razor-Syntaxreferenz für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67a6f-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="67a6f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) und [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="67a6f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="67a6f-105">Razor stellt eine Markupsyntax zum Einbetten von serverbasiertem Code in Webseiten dar.</span><span class="sxs-lookup"><span data-stu-id="67a6f-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="67a6f-106">Die Razor-Syntax besteht aus dem Razor-Markup, C# und HTML.</span><span class="sxs-lookup"><span data-stu-id="67a6f-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="67a6f-107">Dateien, die Razor enthalten, besitzen in der Regel die Dateierweiterung *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="67a6f-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="67a6f-108">Razor ist auch in [Razor-Komponentendateien](xref:blazor/components) (*.razor*) enthalten.</span><span class="sxs-lookup"><span data-stu-id="67a6f-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="67a6f-109">Rendern von HTML</span><span class="sxs-lookup"><span data-stu-id="67a6f-109">Rendering HTML</span></span>

<span data-ttu-id="67a6f-110">Die Standardsprache für Razor ist HTML.</span><span class="sxs-lookup"><span data-stu-id="67a6f-110">The default Razor language is HTML.</span></span> <span data-ttu-id="67a6f-111">Das Rendern von HTML-Code aus einem Razor-Markup funktioniert genauso wie das Rendern von HTML aus einer HTML-Datei.</span><span class="sxs-lookup"><span data-stu-id="67a6f-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="67a6f-112">Ein HTML-Markup wird in Razor-Dateien mit der Erweiterung *.cshtml* vom Server unverändert gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="67a6f-113">Razor-Syntax</span><span class="sxs-lookup"><span data-stu-id="67a6f-113">Razor syntax</span></span>

<span data-ttu-id="67a6f-114">Razor unterstützt C# und verwendet für den Übergang von HTML zu C# das `@`-Symbol.</span><span class="sxs-lookup"><span data-stu-id="67a6f-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="67a6f-115">Razor wertet C#-Ausdrücke aus und rendert sie in der HTML-Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="67a6f-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="67a6f-116">Folgt auf ein `@`-Symbol [ein für Razor reserviertes Schlüsselwort](#razor-reserved-keywords), wechselt es in ein Razor-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="67a6f-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="67a6f-117">Andernfalls erfolgt der Übergang in normales C#.</span><span class="sxs-lookup"><span data-stu-id="67a6f-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="67a6f-118">Verwenden Sie ein zweites `@`-Symbol, um im Razor-Markup ein `@`-Symbol mit Escapezeichen zu versehen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="67a6f-119">Der Code wird in HTML mit einem einzelnen `@`-Symbol gerendert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="67a6f-120">Bei HTML-Attributen und Inhalt mit E-Mail-Adressen wird das `@`-Symbol nicht als ein Übergangszeichen behandelt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="67a6f-121">Die E-Mail-Adressen im folgenden Beispiel bleiben von der Razor-Analyse unverändert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="67a6f-122">Implizite Razor-Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="67a6f-122">Implicit Razor expressions</span></span>

<span data-ttu-id="67a6f-123">Implizite Razor-Ausdrücke beginnen mit `@` gefolgt von C#-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="67a6f-124">Mit Ausnahme des C#-Schlüsselworts `await` dürfen implizite Ausdrücke keine Leerzeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="67a6f-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="67a6f-125">Wird die C#-Anweisung eindeutig beendet, können auch Leerzeichen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="67a6f-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="67a6f-126">Implizite Ausdrücke dürfen **keine** C#-Generics enthalten, da die Zeichen innerhalb der Klammern (`<>`) als HTML-Tag interpretiert werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="67a6f-127">Der folgende Code ist **ungültig**:</span><span class="sxs-lookup"><span data-stu-id="67a6f-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="67a6f-128">Der vorhergehende Code erzeugt einen Compilerfehler, der folgendermaßen aussehen kann:</span><span class="sxs-lookup"><span data-stu-id="67a6f-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="67a6f-129">Das Element „int“ wurde nicht geschlossen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="67a6f-130">Alle Elemente müssen entweder selbstschließend sein oder ein zugehöriges Endtag besitzen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="67a6f-131">Die Methodengruppe „GenericMethod“ kann nicht in den Nichtdelegattyp „Objekt“ konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="67a6f-132">Wollten Sie die Methode aufrufen?</span><span class="sxs-lookup"><span data-stu-id="67a6f-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="67a6f-133">Generische Methodenaufrufe müssen von einem [expliziten Razor-Ausdruck](#explicit-razor-expressions) oder einem [Razor-Codeblock](#razor-code-blocks) umschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="67a6f-134">Explizite Razor-Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="67a6f-134">Explicit Razor expressions</span></span>

<span data-ttu-id="67a6f-135">Explizite Razor-Ausdrücke bestehen aus einem `@`-Symbol mit ausgeglichener Klammer.</span><span class="sxs-lookup"><span data-stu-id="67a6f-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="67a6f-136">Zum Rendern der Uhrzeit von letzter Woche wird folgendes Razor-Markup verwendet:</span><span class="sxs-lookup"><span data-stu-id="67a6f-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="67a6f-137">Jeglicher Inhalt innerhalb der `@()`-Klammer wird ausgewertet und in der Ausgabe gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="67a6f-138">Die im vorherigen Abschnitt beschriebenen impliziten Ausdrücke dürfen grundsätzlich keine Leerzeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="67a6f-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="67a6f-139">Im folgenden Code wird eine Woche nicht von der aktuellen Uhrzeit abgezogen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="67a6f-140">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="67a6f-141">Explizite Ausdrücke können zum Verketten von Text mit einem Ergebnis des Ausdrucks verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="67a6f-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="67a6f-142">Ohne den expliziten Ausdruck wird `<p>Age@joe.Age</p>` als E-Mail-Adresse behandelt und `<p>Age@joe.Age</p>` gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="67a6f-143">`<p>Age33</p>` wird gerendert, wenn es als expliziter Ausdruck geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="67a6f-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="67a6f-144">Explizite Ausdrücke können zum Rendern der Ausgabe von generischen Methoden in *.cshtml*-Dateien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="67a6f-145">Das folgende Markup zeigt, wie der weiter oben gezeigte Fehler behoben wird, der durch die Klammern einer generischen C#-Funktion verursacht wurde.</span><span class="sxs-lookup"><span data-stu-id="67a6f-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="67a6f-146">Der Code wird als expliziter Ausdruck geschrieben:</span><span class="sxs-lookup"><span data-stu-id="67a6f-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="67a6f-147">Codieren von Ausdrücken</span><span class="sxs-lookup"><span data-stu-id="67a6f-147">Expression encoding</span></span>

<span data-ttu-id="67a6f-148">C#-Ausdrücke, die als Zeichenfolge ausgewertet werden, werden HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="67a6f-149">C#-Ausdrücke, die als `IHtmlContent` ausgewertet werden, werden direkt durch `IHtmlContent.WriteTo` gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="67a6f-150">C#-Ausdrücke, die nicht als `IHtmlContent` ausgewertet werden, werden durch `ToString` in eine Zeichenfolge konvertiert und vor dem Rendern codiert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="67a6f-151">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="67a6f-152">Der HTML-Code wird im Browser folgendermaßen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-152">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="67a6f-153">Die Ausgabe `HtmlHelper.Raw` wird nicht codiert, sondern als HTML-Markup gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="67a6f-154">Die Verwendung von `HtmlHelper.Raw` bei einer nicht bereinigten Benutzereingabe stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="67a6f-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="67a6f-155">Benutzereingaben können schädlichen JavaScript-Code oder andere Sicherheitsrisiken enthalten.</span><span class="sxs-lookup"><span data-stu-id="67a6f-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="67a6f-156">Das Bereinigen von Benutzereingaben ist schwierig.</span><span class="sxs-lookup"><span data-stu-id="67a6f-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="67a6f-157">Vermeiden Sie daher die Verwendung von `HtmlHelper.Raw` bei Benutzereingaben.</span><span class="sxs-lookup"><span data-stu-id="67a6f-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="67a6f-158">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="67a6f-159">Razor-Codeblöcke</span><span class="sxs-lookup"><span data-stu-id="67a6f-159">Razor code blocks</span></span>

<span data-ttu-id="67a6f-160">Razor-Codeblöcke beginnen mit `@` und sind von `{}` eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="67a6f-161">Im Gegensatz zu Ausdrücken wird C#-Code in Codeblöcken nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="67a6f-162">Codeblöcke und Ausdrücke in einer Ansicht nutzen den gleichen Bereich und werden der Reihe nach definiert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="67a6f-163">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a6f-164">Deklarieren Sie in Codeblöcken [lokale Funktionen](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) mit Markup als Vorlagenmethoden:</span><span class="sxs-lookup"><span data-stu-id="67a6f-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="67a6f-165">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="67a6f-166">Implizite Übergänge</span><span class="sxs-lookup"><span data-stu-id="67a6f-166">Implicit transitions</span></span>

<span data-ttu-id="67a6f-167">Die Standardsprache in einem Codeblock ist C#. Die Razor Page kann jedoch zurück zu HTML wechseln:</span><span class="sxs-lookup"><span data-stu-id="67a6f-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="67a6f-168">Durch Trennzeichen getrennte explizite Übergänge</span><span class="sxs-lookup"><span data-stu-id="67a6f-168">Explicit delimited transition</span></span>

<span data-ttu-id="67a6f-169">Soll ein Unterabschnitt eines Codeblocks in HTML gerendert werden, umschließen Sie die Zeichen, die gerendert werden sollen, mit dem Razor-Tag `<text>`:</span><span class="sxs-lookup"><span data-stu-id="67a6f-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="67a6f-170">Verwenden Sie diese Methode zum Rendern von HTML-Code, der nicht von einem HTML-Tag umschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="67a6f-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="67a6f-171">Ohne HTML- oder Razor-Tag tritt ein Razor-Laufzeitfehler auf.</span><span class="sxs-lookup"><span data-stu-id="67a6f-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="67a6f-172">Das `<text>`-Tag ist nützlich, wenn Sie beim Rendern von Inhalt Leerzeichen steuern möchten:</span><span class="sxs-lookup"><span data-stu-id="67a6f-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="67a6f-173">Nur der Inhalt zwischen den `<text>`-Tags wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="67a6f-174">In der HTML-Ausgabe werden keine Leerzeichen vor oder nach dem `<text>`-Tag angezeigt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="67a6f-175">Explizite Zeilenübergänge</span><span class="sxs-lookup"><span data-stu-id="67a6f-175">Explicit line transition</span></span>

<span data-ttu-id="67a6f-176">Verwenden Sie die `@:`-Syntax, um den Rest einer kompletten Zeile als HTML-Code in einem Codeblock zu rendern:</span><span class="sxs-lookup"><span data-stu-id="67a6f-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="67a6f-177">Ohne das `@:`-Symbol im Code wird ein Razor-Laufzeitfehler erzeugt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="67a6f-178">Zusätzliche `@`-Zeichen in einer Razor-Datei können zu Compilerfehlern bei späteren Anweisungen im Block führen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="67a6f-179">Diese Compilerfehler können dann schwer nachvollziehbar sein, da der tatsächliche vor dem gemeldeten Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="67a6f-180">Dieser Fehler tritt häufig auf, wenn mehrere implizite/explizite Ausdrücke in einem einzigen Codeblock kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="67a6f-181">Steuerungsstrukturen</span><span class="sxs-lookup"><span data-stu-id="67a6f-181">Control structures</span></span>

<span data-ttu-id="67a6f-182">Steuerungsstrukturen sind eine Erweiterung von Codeblöcken.</span><span class="sxs-lookup"><span data-stu-id="67a6f-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="67a6f-183">Alle Aspekte von Codeblöcken (Übergang zu Markup, Inline-C#) gelten auch für die folgenden Strukturen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="67a6f-184">Die Bedingungen \@if, else if, else und \@switch</span><span class="sxs-lookup"><span data-stu-id="67a6f-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="67a6f-185">`@if` steuert, wann der Code ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="67a6f-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="67a6f-186">`else` und `else if` erfordern kein `@`-Symbol:</span><span class="sxs-lookup"><span data-stu-id="67a6f-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="67a6f-187">Im folgenden Markup wird die Verwendung einer switch-Anweisung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="67a6f-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="67a6f-188">Schleifen \@for, \@foreach, \@while und \@do while</span><span class="sxs-lookup"><span data-stu-id="67a6f-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="67a6f-189">Auf Vorlagen basierender HTML-Code kann mit Anweisungen zur Steuerung von Schleifen gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="67a6f-190">So rendern Sie eine Liste mit Personen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-190">To render a list of people:</span></span>

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

<span data-ttu-id="67a6f-191">Die folgenden Schleifenanweisungen werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="67a6f-192">Verbund \@using</span><span class="sxs-lookup"><span data-stu-id="67a6f-192">Compound \@using</span></span>

<span data-ttu-id="67a6f-193">In C# kann mit einer `using`-Anweisung sichergestellt werden, dass ein Objekt freigegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="67a6f-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="67a6f-194">In Razor wird derselbe Mechanismus verwendet, um HTML-Hilfsprogramme zu erstellen, die zusätzliche Inhalte enthalten.</span><span class="sxs-lookup"><span data-stu-id="67a6f-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="67a6f-195">Im folgenden Code wird ein `<form>`-Tag mit der `@using`-Anweisung durch HTML-Hilfsprogramme gerendert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="67a6f-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="67a6f-196">\@try, catch, finally</span></span>

<span data-ttu-id="67a6f-197">Die Behandlung von Ausnahmen ist vergleichbar mit C#:</span><span class="sxs-lookup"><span data-stu-id="67a6f-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="67a6f-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="67a6f-198">\@lock</span></span>

<span data-ttu-id="67a6f-199">Razor kann kritische Abschnitte mit lock-Anweisungen schützen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="67a6f-200">Kommentare</span><span class="sxs-lookup"><span data-stu-id="67a6f-200">Comments</span></span>

<span data-ttu-id="67a6f-201">Razor unterstützt C#- und HTML-Kommentare:</span><span class="sxs-lookup"><span data-stu-id="67a6f-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="67a6f-202">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="67a6f-203">Razor-Kommentare werden vom Server entfernt, bevor die Webseite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="67a6f-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="67a6f-204">Zur Abgrenzung der Kommentare verwendet Razor `@*  *@`.</span><span class="sxs-lookup"><span data-stu-id="67a6f-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="67a6f-205">Der folgende Code ist auskommentiert, damit vom Server kein Markup gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="67a6f-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="67a6f-206">Anweisungen</span><span class="sxs-lookup"><span data-stu-id="67a6f-206">Directives</span></span>

<span data-ttu-id="67a6f-207">Razor-Anweisungen werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem `@`-Symbol dargestellt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="67a6f-208">Eine Anweisung ändert in der Regel die Analyse einer Ansicht oder aktiviert unterschiedliche Funktionen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="67a6f-209">Wenn Sie wissen, wie von Razor Code für eine Ansicht generiert wird, erleichtert dies das Verständnis dafür, wie Anweisungen funktionieren.</span><span class="sxs-lookup"><span data-stu-id="67a6f-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="67a6f-210">Durch den Code wird eine Klasse ähnlich der folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="67a6f-211">Im Abschnitt [Überprüfen der Razor-C#-Klasse, die für eine Ansicht generiert wurde](#inspect-the-razor-c-class-generated-for-a-view) weiter unten wird erklärt, wie die generierte Klasse angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="67a6f-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="67a6f-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="67a6f-212">\@attribute</span></span>

<span data-ttu-id="67a6f-213">Die `@attribute`-Anweisung fügt das angegebene Attribut zu der Klasse der generierten Seite oder Ansicht hinzu.</span><span class="sxs-lookup"><span data-stu-id="67a6f-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="67a6f-214">Im folgenden Beispiel wird das `[Authorize]`-Attribut hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="67a6f-215">\@code</span><span class="sxs-lookup"><span data-stu-id="67a6f-215">\@code</span></span>

<span data-ttu-id="67a6f-216">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-217">Der `@code`-Block ermöglicht einer [Razor-Komponente](xref:blazor/components) das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zu einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="67a6f-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="67a6f-218">Für Razor-Komponenten ist `@code` ein Alias von [`@functions`](#functions) und wird über `@functions` empfohlen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="67a6f-219">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="67a6f-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="67a6f-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="67a6f-220">\@functions</span></span>

<span data-ttu-id="67a6f-221">Die `@functions`-Anweisung ermöglicht das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zur generierten Klasse:</span><span class="sxs-lookup"><span data-stu-id="67a6f-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a6f-222">Verwenden Sie in [Razor-Komponenten](xref:blazor/components) `@code` über `@functions`, um C#-Member hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="67a6f-223">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="67a6f-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="67a6f-224">Der Code generiert das folgende HTML-Markup:</span><span class="sxs-lookup"><span data-stu-id="67a6f-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="67a6f-225">Der folgende Code wird in der Razor-C#-Klasse generiert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a6f-226">`@functions`-Methoden dienen als Vorlagenmethoden, wenn sie Markup aufweisen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="67a6f-227">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="67a6f-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="67a6f-228">\@implements</span></span>

<span data-ttu-id="67a6f-229">Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.</span><span class="sxs-lookup"><span data-stu-id="67a6f-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="67a6f-230">Im folgenden Beispiel wird <xref:System.IDisposable?displayProperty=fullName> implementiert, sodass die <xref:System.IDisposable.Dispose*>-Methode aufgerufen werden kann:</span><span class="sxs-lookup"><span data-stu-id="67a6f-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="67a6f-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="67a6f-231">\@inherits</span></span>

<span data-ttu-id="67a6f-232">Die `@inherits`-Anweisung bietet uneingeschränkten Zugriff auf die Klasse, die die Ansicht erbt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="67a6f-233">Der folgende Code ist ein benutzerdefinierter Typ der Razor Page:</span><span class="sxs-lookup"><span data-stu-id="67a6f-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="67a6f-234">`CustomText` wird in einer Ansicht angezeigt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="67a6f-235">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="67a6f-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="67a6f-236">`@model` und `@inherits` können in derselben Ansicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="67a6f-237">`@inherits` kann in einer *_ViewImports.cshtml*-Datei verwendet werden, die von der Ansicht importiert wird:</span><span class="sxs-lookup"><span data-stu-id="67a6f-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="67a6f-238">Der folgende Code ist ein Beispiel für eine stark typisierte Ansicht:</span><span class="sxs-lookup"><span data-stu-id="67a6f-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="67a6f-239">Wird „rick@contoso.com“ im Modell übergeben, generiert die Ansicht das folgende HTML-Markup:</span><span class="sxs-lookup"><span data-stu-id="67a6f-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="67a6f-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="67a6f-240">\@inject</span></span>

<span data-ttu-id="67a6f-241">Mit der `@inject`-Anweisung kann die Razor Page einen Dienst aus dem [Dienstcontainer](xref:fundamentals/dependency-injection) in eine Ansicht einfügen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="67a6f-242">Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67a6f-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="67a6f-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="67a6f-243">\@layout</span></span>

<span data-ttu-id="67a6f-244">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-245">Die `@layout`-Anweisung gibt ein Layout für eine Razor-Komponente an.</span><span class="sxs-lookup"><span data-stu-id="67a6f-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="67a6f-246">Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="67a6f-247">Weitere Informationen finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="67a6f-248">\@model</span><span class="sxs-lookup"><span data-stu-id="67a6f-248">\@model</span></span>

<span data-ttu-id="67a6f-249">*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="67a6f-250">Die `@model`-Anweisung gibt den Typ des Modells an, das an eine Ansicht oder Seite übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="67a6f-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="67a6f-251">In einer mit einzelnen Benutzerkonten erstellten ASP.NET Core MVC- oder Razor Pages-App enthält *Views/Account/Login.cshtml* die folgende Modelldeklaration:</span><span class="sxs-lookup"><span data-stu-id="67a6f-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="67a6f-252">Die generierte Klasse erbt von `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="67a6f-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="67a6f-253">Razor macht eine `Model`-Eigenschaft für den Zugriff auf das an die Ansicht übergebene Modell verfügbar:</span><span class="sxs-lookup"><span data-stu-id="67a6f-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="67a6f-254">Die `@model`-Anweisung gibt den Typ der `Model`-Eigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="67a6f-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="67a6f-255">Die Anweisung legt das `T` in `RazorPage<T>` der generierten Klasse fest, von der die Ansicht abgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="67a6f-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="67a6f-256">Wird die `@model`-Anweisung nicht angegeben, hat die `Model`-Eigenschaft den Typ `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="67a6f-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="67a6f-257">Weitere Informationen finden Sie unter [Stark typisierte Modelle und das Schlüsselwort @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="67a6f-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="67a6f-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="67a6f-258">\@namespace</span></span>

<span data-ttu-id="67a6f-259">Die `@namespace`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="67a6f-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="67a6f-260">Legt den Namespace der Klasse der generierten Razor-Seite, der MVC-Ansicht oder der Razor-Komponente fest.</span><span class="sxs-lookup"><span data-stu-id="67a6f-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="67a6f-261">Legt die vom Stamm abgeleiteten Namespaces einer Seiten-, Ansichts- oder Komponentenklasse aus der nächsten Importdatei in der Verzeichnisstruktur fest: *_ViewImports.cshtml*. (Ansichten oder Seiten) oder *_Imports.razor* (Razor-Komponenten).</span><span class="sxs-lookup"><span data-stu-id="67a6f-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="67a6f-262">Für das Razor Pages Beispiel in der folgenden Tabelle gilt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="67a6f-263">Jede Seite importiert *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="67a6f-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="67a6f-264">*Pages/_ViewImports.cshtml* enthält `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="67a6f-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="67a6f-265">Jede Seite weist `Hello.World` als Stamm ihres Namespace auf.</span><span class="sxs-lookup"><span data-stu-id="67a6f-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="67a6f-266">Seite</span><span class="sxs-lookup"><span data-stu-id="67a6f-266">Page</span></span>                                        | <span data-ttu-id="67a6f-267">Namespace</span><span class="sxs-lookup"><span data-stu-id="67a6f-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="67a6f-268">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="67a6f-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="67a6f-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="67a6f-271">Die vorstehenden Beziehungen gelten für Importdateien, die mit MVC-Ansichten und Razor-Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="67a6f-272">Wenn mehrere Importdateien über eine `@namespace`-Anweisung verfügen, wird die Datei verwendet, die der Seite, der Ansicht oder der Komponente in der Verzeichnisstruktur am nächsten ist, um den Stammnamespace festzulegen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="67a6f-273">Wenn der Ordner *EvenMorePages* im Beispiel oben eine Importdatei mit `@namespace Another.Planet` enthält (oder die Datei *Pages/MorePages/EvenMorePages/Page.cshtml* `@namespace Another.Planet` enthält), finden Sie das Ergebnis in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="67a6f-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="67a6f-274">Seite</span><span class="sxs-lookup"><span data-stu-id="67a6f-274">Page</span></span>                                        | <span data-ttu-id="67a6f-275">Namespace</span><span class="sxs-lookup"><span data-stu-id="67a6f-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="67a6f-276">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="67a6f-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="67a6f-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="67a6f-279">\@page</span><span class="sxs-lookup"><span data-stu-id="67a6f-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a6f-280">Die `@page`-Anweisung hat abhängig vom Typ der Datei, in der Sie verwendet wird, unterschiedliche Auswirkungen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="67a6f-281">Für die Anweisung gilt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-281">The directive:</span></span>

* <span data-ttu-id="67a6f-282">In einer *CSHTML*-Datei gibt sie an, dass die Datei eine Razor-Seite ist.</span><span class="sxs-lookup"><span data-stu-id="67a6f-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="67a6f-283">Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes) und <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="67a6f-284">Gibt an, dass eine Razor-Komponente Anforderungen direkt verarbeiten soll.</span><span class="sxs-lookup"><span data-stu-id="67a6f-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="67a6f-285">Weitere Informationen finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67a6f-286">Die `@page`-Anweisung in der ersten Zeile einer *CSHTML*-Datei gibt an, dass die Datei eine Razor-Seite ist.</span><span class="sxs-lookup"><span data-stu-id="67a6f-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="67a6f-287">Weitere Informationen finden Sie unter <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="67a6f-288">\@section</span><span class="sxs-lookup"><span data-stu-id="67a6f-288">\@section</span></span>

<span data-ttu-id="67a6f-289">*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="67a6f-290">Die `@section`-Anweisung wird in Verbindung mit [MVC- und Razor Pages-Layouts](xref:mvc/views/layout) verwendet, damit Ansichten Seiten Inhalte in verschiedenen Teilen der HTML-Seite rendern können.</span><span class="sxs-lookup"><span data-stu-id="67a6f-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="67a6f-291">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="67a6f-292">\@using</span><span class="sxs-lookup"><span data-stu-id="67a6f-292">\@using</span></span>

<span data-ttu-id="67a6f-293">Die `@using`-Anweisung fügt die C#-Anweisung `using`der generierten Ansicht hinzu:</span><span class="sxs-lookup"><span data-stu-id="67a6f-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a6f-294">[ steuert in ](xref:blazor/components)Razor-Komponenten`@using` außerdem, welche Komponenten sich im Gültigkeitsbereich befinden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="67a6f-295">Direktivenattribute</span><span class="sxs-lookup"><span data-stu-id="67a6f-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="67a6f-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="67a6f-296">\@attributes</span></span>

<span data-ttu-id="67a6f-297">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-298">`@attributes` ermöglicht es einer Komponente, nicht deklarierte Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="67a6f-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="67a6f-299">Weitere Informationen finden Sie unter <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="67a6f-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="67a6f-300">\@bind</span></span>

<span data-ttu-id="67a6f-301">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-302">Die Datenbindung in-Komponenten wird mit dem `@bind`-Attribut erreicht.</span><span class="sxs-lookup"><span data-stu-id="67a6f-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="67a6f-303">Weitere Informationen finden Sie unter <xref:blazor/components#data-binding>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-303">For more information, see <xref:blazor/components#data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="67a6f-304">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="67a6f-304">\@on{EVENT}</span></span>

<span data-ttu-id="67a6f-305">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-306">Razor stellt Ereignisbehandlungsfunktionen für Komponenten bereit.</span><span class="sxs-lookup"><span data-stu-id="67a6f-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="67a6f-307">Weitere Informationen finden Sie unter <xref:blazor/components#event-handling>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-307">For more information, see <xref:blazor/components#event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="67a6f-308">\@on{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="67a6f-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="67a6f-309">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-310">Verhindert die Standardaktion für das Ereignis.</span><span class="sxs-lookup"><span data-stu-id="67a6f-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="67a6f-311">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="67a6f-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="67a6f-312">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-313">Beendet die Ereignisweitergabe für das Ereignis.</span><span class="sxs-lookup"><span data-stu-id="67a6f-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="67a6f-314">\@key</span><span class="sxs-lookup"><span data-stu-id="67a6f-314">\@key</span></span>

<span data-ttu-id="67a6f-315">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-316">Das Direktivenattribut `@key` bewirkt, dass der Komponentenvergleichsalgorithmus die Erhaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert.</span><span class="sxs-lookup"><span data-stu-id="67a6f-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="67a6f-317">Weitere Informationen finden Sie unter <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="67a6f-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="67a6f-318">\@ref</span></span>

<span data-ttu-id="67a6f-319">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-320">Komponentenverweise (`@ref`) bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="67a6f-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="67a6f-321">Weitere Informationen finden Sie unter <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="67a6f-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="67a6f-322">\@typeparam</span></span>

<span data-ttu-id="67a6f-323">*Dieses Szenario gilt nur für Razor-Komponenten (.razor).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="67a6f-324">Die `@typeparam`-Anweisung deklariert einen generischen Typparameter für die generierte Komponentenklasse.</span><span class="sxs-lookup"><span data-stu-id="67a6f-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="67a6f-325">Weitere Informationen finden Sie unter <xref:blazor/components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="67a6f-325">For more information, see <xref:blazor/components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="67a6f-326">Auf Vorlagen basierende Razor-Delegate</span><span class="sxs-lookup"><span data-stu-id="67a6f-326">Templated Razor delegates</span></span>

<span data-ttu-id="67a6f-327">Mit Razor-Vorlagen können Sie einen Ausschnitt der Benutzeroberfläche mit dem folgenden Format festlegen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="67a6f-328">Im folgenden Beispiel wird veranschaulicht, wie ein auf Vorlagen basierender Razor-Delegat als <xref:System.Func%602> angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="67a6f-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="67a6f-329">Der Typ [dynamic](/dotnet/csharp/programming-guide/types/using-type-dynamic) wird für den Parameter der Methode angegeben, die der Delegat einkapselt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="67a6f-330">Ein [object](/dotnet/csharp/language-reference/keywords/object)-Typ wird als Rückgabewert des Delegats angegeben.</span><span class="sxs-lookup"><span data-stu-id="67a6f-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="67a6f-331">Die Vorlage wird mit <xref:System.Collections.Generic.List%601> von `Pet` mit einer `Name`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="67a6f-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="67a6f-332">Die Vorlage wird mit `pets` in einer `foreach`-Anweisung gerendert:</span><span class="sxs-lookup"><span data-stu-id="67a6f-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="67a6f-333">Gerenderte Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="67a6f-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="67a6f-334">Sie können auch eine Razor-Inlinevorlage als Argument für eine Methode angeben.</span><span class="sxs-lookup"><span data-stu-id="67a6f-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="67a6f-335">Im folgenden Beispiel nimmt die `Repeat`-Methode eine Razor-Vorlage entgegen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="67a6f-336">Die Methode verwendet die Vorlage dann zum Erstellen von HTML-Inhalten mit Wiederholungen von Elementen aus einer Liste:</span><span class="sxs-lookup"><span data-stu-id="67a6f-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="67a6f-337">Wird die Liste mit Haustieren aus dem vorherigen Beispiel verwendet, wird die `Repeat`-Methode wie folgt aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="67a6f-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="67a6f-338"><xref:System.Collections.Generic.List%601> von `Pet`</span><span class="sxs-lookup"><span data-stu-id="67a6f-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="67a6f-339">Anzahl der Wiederholungen für jedes Haustier</span><span class="sxs-lookup"><span data-stu-id="67a6f-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="67a6f-340">Inlinevorlage zur Verwendung für die Elemente einer unsortierten Liste</span><span class="sxs-lookup"><span data-stu-id="67a6f-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="67a6f-341">Gerenderte Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="67a6f-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="67a6f-342">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="67a6f-342">Tag Helpers</span></span>

<span data-ttu-id="67a6f-343">*Dieses Szenario gilt nur für MVC-Ansichten und Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="67a6f-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="67a6f-344">Die folgenden drei Anweisungen gehören zu den [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="67a6f-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="67a6f-345">Anweisung</span><span class="sxs-lookup"><span data-stu-id="67a6f-345">Directive</span></span> | <span data-ttu-id="67a6f-346">Funktion</span><span class="sxs-lookup"><span data-stu-id="67a6f-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="67a6f-347">Macht Taghilfsprogramme für eine Ansicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="67a6f-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="67a6f-348">Entfernt die zuvor aus einer Ansicht hinzugefügten Taghilfsprogramme.</span><span class="sxs-lookup"><span data-stu-id="67a6f-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="67a6f-349">Gibt ein Tagpräfix an, um Unterstützung für Taghilfsprogramme zu aktivieren und ihre Verwendung explizit festzulegen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="67a6f-350">Für Razor reservierte Schlüsselwörter</span><span class="sxs-lookup"><span data-stu-id="67a6f-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="67a6f-351">Razor-Schlüsselwörter</span><span class="sxs-lookup"><span data-stu-id="67a6f-351">Razor keywords</span></span>

* <span data-ttu-id="67a6f-352">page (erfordert ASP.NET Core 2.1 oder höher)</span><span class="sxs-lookup"><span data-stu-id="67a6f-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="67a6f-353">namespace</span><span class="sxs-lookup"><span data-stu-id="67a6f-353">namespace</span></span>
* <span data-ttu-id="67a6f-354">functions</span><span class="sxs-lookup"><span data-stu-id="67a6f-354">functions</span></span>
* <span data-ttu-id="67a6f-355">inherits</span><span class="sxs-lookup"><span data-stu-id="67a6f-355">inherits</span></span>
* <span data-ttu-id="67a6f-356">model</span><span class="sxs-lookup"><span data-stu-id="67a6f-356">model</span></span>
* <span data-ttu-id="67a6f-357">section</span><span class="sxs-lookup"><span data-stu-id="67a6f-357">section</span></span>
* <span data-ttu-id="67a6f-358">helper (wird derzeit nicht von ASP.NET Core unterstützt)</span><span class="sxs-lookup"><span data-stu-id="67a6f-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="67a6f-359">Razor-Schlüsselwörter werden mit dem Escapezeichen `@(Razor Keyword)` versehen (z.B. `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="67a6f-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="67a6f-360">Razor-C#-Schlüsselwörter</span><span class="sxs-lookup"><span data-stu-id="67a6f-360">C# Razor keywords</span></span>

* <span data-ttu-id="67a6f-361">case</span><span class="sxs-lookup"><span data-stu-id="67a6f-361">case</span></span>
* <span data-ttu-id="67a6f-362">do</span><span class="sxs-lookup"><span data-stu-id="67a6f-362">do</span></span>
* <span data-ttu-id="67a6f-363">default</span><span class="sxs-lookup"><span data-stu-id="67a6f-363">default</span></span>
* <span data-ttu-id="67a6f-364">for</span><span class="sxs-lookup"><span data-stu-id="67a6f-364">for</span></span>
* <span data-ttu-id="67a6f-365">foreach</span><span class="sxs-lookup"><span data-stu-id="67a6f-365">foreach</span></span>
* <span data-ttu-id="67a6f-366">if</span><span class="sxs-lookup"><span data-stu-id="67a6f-366">if</span></span>
* <span data-ttu-id="67a6f-367">else</span><span class="sxs-lookup"><span data-stu-id="67a6f-367">else</span></span>
* <span data-ttu-id="67a6f-368">lock</span><span class="sxs-lookup"><span data-stu-id="67a6f-368">lock</span></span>
* <span data-ttu-id="67a6f-369">switch</span><span class="sxs-lookup"><span data-stu-id="67a6f-369">switch</span></span>
* <span data-ttu-id="67a6f-370">try</span><span class="sxs-lookup"><span data-stu-id="67a6f-370">try</span></span>
* <span data-ttu-id="67a6f-371">catch</span><span class="sxs-lookup"><span data-stu-id="67a6f-371">catch</span></span>
* <span data-ttu-id="67a6f-372">finally</span><span class="sxs-lookup"><span data-stu-id="67a6f-372">finally</span></span>
* <span data-ttu-id="67a6f-373">using</span><span class="sxs-lookup"><span data-stu-id="67a6f-373">using</span></span>
* <span data-ttu-id="67a6f-374">while</span><span class="sxs-lookup"><span data-stu-id="67a6f-374">while</span></span>

<span data-ttu-id="67a6f-375">Razor-C#-Schlüsselwörter werden mit dem doppeltem Escapezeichen `@(@C# Razor Keyword)` versehen (z.B. `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="67a6f-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="67a6f-376">Das erste `@` dient als Escapezeichen für den Razor-Parser.</span><span class="sxs-lookup"><span data-stu-id="67a6f-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="67a6f-377">Das zweite `@` dient als Escapezeichen für den C#-Parser.</span><span class="sxs-lookup"><span data-stu-id="67a6f-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="67a6f-378">Reservierte Schlüsselwörter, die nicht von Razor verwendet werden</span><span class="sxs-lookup"><span data-stu-id="67a6f-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="67a6f-379">class</span><span class="sxs-lookup"><span data-stu-id="67a6f-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="67a6f-380">Überprüfen der Razor-C#-Klasse, die für eine Ansicht generiert wurde</span><span class="sxs-lookup"><span data-stu-id="67a6f-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="67a6f-381">Bei .NET Core SDK 2.1 oder höher führt das [Razor SDK](xref:razor-pages/sdk) die Kompilierung von Razor-Dateien durch.</span><span class="sxs-lookup"><span data-stu-id="67a6f-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="67a6f-382">Beim Erstellen eines Projekts generiert das Razor SDK das Verzeichnis *obj/<build_konfiguration>/<ziel_framework_bezeichnung>/Razor* im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="67a6f-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="67a6f-383">Die Verzeichnisstruktur im *Razor*-Verzeichnis spiegelt die Verzeichnisstruktur des Projekts.</span><span class="sxs-lookup"><span data-stu-id="67a6f-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="67a6f-384">Beachten Sie die folgende Verzeichnisstruktur in einem Razor Pages-Projekt in ASP.NET Core 2.1 für .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="67a6f-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="67a6f-385">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-385">**Areas/**</span></span>
  * <span data-ttu-id="67a6f-386">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-386">**Admin/**</span></span>
    * <span data-ttu-id="67a6f-387">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-387">**Pages/**</span></span>
      * <span data-ttu-id="67a6f-388">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="67a6f-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="67a6f-390">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-390">**Pages/**</span></span>
  * <span data-ttu-id="67a6f-391">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-391">**Shared/**</span></span>
    * <span data-ttu-id="67a6f-392">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="67a6f-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="67a6f-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="67a6f-395">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="67a6f-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="67a6f-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="67a6f-397">Wenn Sie das Projekt mit der Konfiguration zum *Debuggen* erstellen, wird folgendes *obj*-Verzeichnis erstellt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="67a6f-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-398">**obj/**</span></span>
  * <span data-ttu-id="67a6f-399">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-399">**Debug/**</span></span>
    * <span data-ttu-id="67a6f-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="67a6f-401">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-401">**Razor/**</span></span>
        * <span data-ttu-id="67a6f-402">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-402">**Areas/**</span></span>
          * <span data-ttu-id="67a6f-403">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-403">**Admin/**</span></span>
            * <span data-ttu-id="67a6f-404">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-404">**Pages/**</span></span>
              * <span data-ttu-id="67a6f-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="67a6f-406">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-406">**Pages/**</span></span>
          * <span data-ttu-id="67a6f-407">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="67a6f-407">**Shared/**</span></span>
            * <span data-ttu-id="67a6f-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="67a6f-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="67a6f-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="67a6f-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="67a6f-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="67a6f-412">Öffnen Sie *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*, um die für *Pages/Index.cshtml* generierte Klasse anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="67a6f-413">Fügen Sie dem ASP.NET Core MVC-Projekt die folgende Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="67a6f-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="67a6f-414">Überschreiben Sie in `Startup.ConfigureServices` die von MVC hinzugefügte `RazorTemplateEngine`-Klasse mit der `CustomTemplateEngine`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="67a6f-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="67a6f-415">Legen Sie auf der `return csharpDocument;`-Anweisung von `CustomTemplateEngine` einen Haltepunkt fest.</span><span class="sxs-lookup"><span data-stu-id="67a6f-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="67a6f-416">Wenn die Ausführung des Programms am Haltepunkt angehalten wird, können Sie den Wert von `generatedCode` anzeigen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Ansicht „Text-Schnellansicht“ von „generatedCode“](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="67a6f-418">Ansicht der Suchvorgänge und Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="67a6f-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="67a6f-419">Die Razor-Ansichtsengine führt für Ansichten Suchvorgänge aus, die die Groß-/Kleinschreibung berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="67a6f-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="67a6f-420">Der tatsächliche Suchvorgang wird jedoch vom zugrunde liegenden Dateisystem bestimmt:</span><span class="sxs-lookup"><span data-stu-id="67a6f-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="67a6f-421">Dateibasierte Quelle:</span><span class="sxs-lookup"><span data-stu-id="67a6f-421">File based source:</span></span>
  * <span data-ttu-id="67a6f-422">Bei Betriebssystemen, die Dateisysteme ohne Berücksichtigung von Groß-/Kleinschreibung verwenden (z.B. Windows), wird bei Suchvorgängen nach physischen Dateianbietern die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="67a6f-423">`return View("Test")` liefert beispielsweise Treffer für */Views/Home/Test.cshtml*, */Views/home/test.cshtml* sowie für jede andere Schreibweise.</span><span class="sxs-lookup"><span data-stu-id="67a6f-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="67a6f-424">Bei Dateisystemen, die die Groß-/Kleinschreibung berücksichtigen (z.B. Linux, OSX sowie mit `EmbeddedFileProvider`), wird die Groß-/Kleinschreibung auch bei Suchvorgängen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="67a6f-425">`return View("Test")` liefert beispielsweise ganz konkret Treffer für */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="67a6f-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="67a6f-426">Vorkompilierte Ansichten: Ab ASP.NET Core 2.0 wird bei der Suche nach vorkompilierten Ansichten unter allen Betriebssystemen die Groß-/Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="67a6f-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="67a6f-427">Das Verhalten ist mit dem des physischen Dateianbieters unter Windows identisch.</span><span class="sxs-lookup"><span data-stu-id="67a6f-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="67a6f-428">Unterscheiden sich zwei vorkompilierte Ansichten nur in der Groß-/Kleinschreibung, ist das Ergebnis der Suche nicht deterministisch.</span><span class="sxs-lookup"><span data-stu-id="67a6f-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="67a6f-429">Entwicklern wird empfohlen, sich bei der Groß-/Kleinschreibung von Datei- und Verzeichnisnamen an der Schreibweise folgender Begriffe zu orientieren:</span><span class="sxs-lookup"><span data-stu-id="67a6f-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="67a6f-430">Bereichs-, Controller- und Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="67a6f-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="67a6f-431">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="67a6f-431">Razor Pages.</span></span>

<span data-ttu-id="67a6f-432">Durch Überprüfung der Groß-/Kleinschreibung wird sichergestellt, dass die entsprechenden Ansichten für die Bereitstellungen unabhängig von dem zugrunde liegenden Dateisystem gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="67a6f-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
