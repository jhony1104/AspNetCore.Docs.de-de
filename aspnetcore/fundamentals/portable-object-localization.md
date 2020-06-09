---
title: 'title: Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core author: sebastienros description: Dieser Artikel führt portable Objektdateien ein und erläutert die Schritte zu ihrer Verwendung in einer ASP.NET Core-Anwendung mit dem Framework Orchard Core.'
author: sebastienros
description: 'ms.author: scaddie ms.date: 09/26/2017 no-loc:'
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355083"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="ac328-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ac328-103">'Blazor'</span></span>

<span data-ttu-id="ac328-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ac328-104">'Identity'</span></span>

<span data-ttu-id="ac328-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ac328-105">'Let's Encrypt'</span></span>

<span data-ttu-id="ac328-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ac328-106">'Razor'</span></span> <span data-ttu-id="ac328-107">'SignalR' uid: fundamentals/portable-object-localization</span><span class="sxs-lookup"><span data-stu-id="ac328-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

<span data-ttu-id="ac328-108">Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac328-108">Configure portable object localization in ASP.NET Core</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="ac328-109">Von [Sébastien Ros](https://github.com/sebastienros) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ac328-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ac328-110">Dieser Artikel erläutert die Schritte zur Verwendung von portablen Objektdateien (PO) in einer ASP.NET Core-Anwendung mit dem Framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="ac328-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span> <span data-ttu-id="ac328-111">**Hinweis**: Orchard Core ist kein Microsoft-Produkt.</span><span class="sxs-lookup"><span data-stu-id="ac328-111">**Note:** Orchard Core isn't a Microsoft product.</span></span>
- <span data-ttu-id="ac328-112">Microsoft bietet daher keinen Support für dieses Feature an.</span><span class="sxs-lookup"><span data-stu-id="ac328-112">Consequently, Microsoft provides no support for this feature.</span></span>
- <span data-ttu-id="ac328-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac328-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="ac328-114">Was ist eine PO-Datei?</span><span class="sxs-lookup"><span data-stu-id="ac328-114">What is a PO file?</span></span>
- <span data-ttu-id="ac328-115">PO-Dateien werden als Textdateien verteilt, die übersetzte Zeichenfolgen für eine bestimmte Sprache enthalten.</span><span class="sxs-lookup"><span data-stu-id="ac328-115">PO files are distributed as text files containing the translated strings for a given language.</span></span>

### <a name="example"></a><span data-ttu-id="ac328-116">Einige Vorteile der Verwendung von PO-Dateien im Vergleich zu *RESX*-Dateien sind:</span><span class="sxs-lookup"><span data-stu-id="ac328-116">Some advantages of using PO files instead *.resx* files include:</span></span>

<span data-ttu-id="ac328-117">PO-Dateien unterstützen Pluralisierung; *RESX*-Dateien unterstützen sie nicht.</span><span class="sxs-lookup"><span data-stu-id="ac328-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>

<span data-ttu-id="ac328-118">PO-Dateien werden nicht wie *RESX*-Dateien kompiliert.</span><span class="sxs-lookup"><span data-stu-id="ac328-118">PO files aren't compiled like *.resx* files.</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="ac328-119">Daher sind spezialisierte Tools und die Buildschritte nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ac328-119">As such, specialized tooling and build steps aren't required.</span></span>

- <span data-ttu-id="ac328-120">PO-Dateien funktionieren gut mit gemeinsamen Onlinebearbeitungstools.</span><span class="sxs-lookup"><span data-stu-id="ac328-120">PO files work well with collaborative online editing tools.</span></span> <span data-ttu-id="ac328-121">Beispiel</span><span class="sxs-lookup"><span data-stu-id="ac328-121">Example</span></span>
- <span data-ttu-id="ac328-122">Hier sehen Sie eine PO-Beispieldatei, die die Übersetzung für zwei Zeichenfolgen in Französisch, einschließlich der Pluralform einer der Zeichenfolgen enthält:</span><span class="sxs-lookup"><span data-stu-id="ac328-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>
- <span data-ttu-id="ac328-123">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="ac328-123">*fr.po*</span></span>

<span data-ttu-id="ac328-124">In diesem Beispiel wird die folgende Syntax verwendet:</span><span class="sxs-lookup"><span data-stu-id="ac328-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="ac328-125">`#:`: ein Kommentar, der den Kontext der zu übersetzenden Zeichenfolge angibt.</span><span class="sxs-lookup"><span data-stu-id="ac328-125">`#:`: A comment indicating the context of the string to be translated.</span></span>
- <span data-ttu-id="ac328-126">Die gleiche Zeichenfolge wird möglicherweise anders übersetzt, je nachdem, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ac328-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="ac328-127">`msgid`: die nicht übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ac328-127">`msgid`: The untranslated string.</span></span>

<span data-ttu-id="ac328-128">`msgstr`: die übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ac328-128">`msgstr`: The translated string.</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="ac328-129">Wenn die Pluralisierung unterstützt wird, können mehrere Einträge definiert werden.</span><span class="sxs-lookup"><span data-stu-id="ac328-129">In the case of pluralization support, more entries can be defined.</span></span>

<span data-ttu-id="ac328-130">`msgid_plural`: die nicht übersetzte Zeichenfolge im Plural.</span><span class="sxs-lookup"><span data-stu-id="ac328-130">`msgid_plural`: The untranslated plural string.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="ac328-131">`msgstr[0]`: die übersetzte Zeichenfolge für den Fall 0.</span><span class="sxs-lookup"><span data-stu-id="ac328-131">`msgstr[0]`: The translated string for the case 0.</span></span>

<span data-ttu-id="ac328-132">`msgstr[N]`: die übersetzte Zeichenfolge für den Fall N.</span><span class="sxs-lookup"><span data-stu-id="ac328-132">`msgstr[N]`: The translated string for the case N.</span></span> <span data-ttu-id="ac328-133">Die PO-Dateispezifikation finden Sie [hier](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="ac328-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

<span data-ttu-id="ac328-134">Konfigurieren der PO-Dateiunterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac328-134">Configuring PO file support in ASP.NET Core</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="ac328-135">Dieses Beispiel beruht auf einer ASP.NET Core MVC-Anwendung, die aus einer Visual Studio 2017-Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="ac328-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

<span data-ttu-id="ac328-136">Verweisen auf das Paket</span><span class="sxs-lookup"><span data-stu-id="ac328-136">Referencing the package</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="ac328-137">Fügen Sie einen Verweis auf das NuGet-Paket `OrchardCore.Localization.Core` hinzu.</span><span class="sxs-lookup"><span data-stu-id="ac328-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="ac328-138">Dieses ist auf [MyGet](https://www.myget.org/) unter der Paketquelle https://www.myget.org/F/orchardcore-preview/api/v3/index.json verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ac328-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span> <span data-ttu-id="ac328-139">Die *CSPROJ*-Datei enthält nun eine Zeile, die der folgenden Zeile ähnelt (die Versionsnummer kann davon abweichen):</span><span class="sxs-lookup"><span data-stu-id="ac328-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="ac328-140">Registrieren des Diensts</span><span class="sxs-lookup"><span data-stu-id="ac328-140">Registering the service</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="ac328-141">Fügen Sie die erforderlichen Dienste zur `ConfigureServices`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="ac328-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="ac328-142">Fügen Sie die erforderliche Middleware zur `Configure`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="ac328-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span> <span data-ttu-id="ac328-143">Fügen Sie den folgenden Code zur Razor-Ansicht Ihrer Wahl hinzu.</span><span class="sxs-lookup"><span data-stu-id="ac328-143">Add the following code to your Razor view of choice.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="ac328-144">In diesem Beispiel wird *About.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="ac328-144">*About.cshtml* is used in this example.</span></span> <span data-ttu-id="ac328-145">Eine `IViewLocalizer`-Instanz wird eingefügt und zum Übersetzen des Texts „Hello world!“ verwendet.</span><span class="sxs-lookup"><span data-stu-id="ac328-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span> <span data-ttu-id="ac328-146">Erstellen einer PO-Datei</span><span class="sxs-lookup"><span data-stu-id="ac328-146">Creating a PO file</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="ac328-147">Erstellen Sie eine Datei mit dem Namen *\<culture code>.po* im Stammordner Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="ac328-147">Create a file named *\<culture code>.po* in your application root folder.</span></span>

<span data-ttu-id="ac328-148">In diesem Beispiel lautet der Dateiname *fr.po*, weil die französische Sprache verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="ac328-148">In this example, the file name is *fr.po* because the French language is used:</span></span> <span data-ttu-id="ac328-149">Diese Datei speichert die zu übersetzende Zeichenfolge sowie die französische Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="ac328-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="ac328-150">Übersetzungen stellen ihre übergeordnete Kultur wieder her, wenn erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ac328-150">Translations revert to their parent culture, if necessary.</span></span>

<span data-ttu-id="ac328-151">In diesem Beispiel wird die *fr.po*-Datei verwendet, wenn die angeforderte Kultur `fr-FR` oder `fr-CA` ist.</span><span class="sxs-lookup"><span data-stu-id="ac328-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span> <span data-ttu-id="ac328-152">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="ac328-152">Testing the application</span></span> <span data-ttu-id="ac328-153">Führen Sie Ihre Anwendung aus, und navigieren Sie zur URL`/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="ac328-153">Run your application, and navigate to the URL `/Home/About`.</span></span>

## <a name="pluralization"></a><span data-ttu-id="ac328-154">Der Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="ac328-154">The text **Hello world!**</span></span>

<span data-ttu-id="ac328-155">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ac328-155">is displayed.</span></span> <span data-ttu-id="ac328-156">Navigieren Sie zur URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="ac328-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span>

<span data-ttu-id="ac328-157">Der Text **Bonjour le Monde!**</span><span class="sxs-lookup"><span data-stu-id="ac328-157">The text **Bonjour le monde!**</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="ac328-158">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ac328-158">is displayed.</span></span>

<span data-ttu-id="ac328-159">Pluralisierung</span><span class="sxs-lookup"><span data-stu-id="ac328-159">Pluralization</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="ac328-160">PO-Dateien unterstützen Pluralformen. Dies ist nützlich, wenn die gleiche Zeichenfolge aufgrund einer Kardinalität unterschiedlich übersetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="ac328-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="ac328-161">Diese Aufgabe ist komplizierter, da jede Sprache benutzerdefinierte Regeln festlegt, um auf der Grundlage von Kardinalität auszuwählen, welche Zeichenfolge zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="ac328-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="ac328-162">Das Orchard Localization-Paket stellt eine API zur Verfügung, um diese anderen Pluralformen automatisch aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ac328-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span> <span data-ttu-id="ac328-163">Erstellen von PO-Pluraldateien</span><span class="sxs-lookup"><span data-stu-id="ac328-163">Creating pluralization PO files</span></span> <span data-ttu-id="ac328-164">Fügen Sie den folgenden Inhalt zur zuvor erwähnten *fr.po*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="ac328-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

<span data-ttu-id="ac328-165">Unter [Was ist eine PO-Datei?](#what-is-a-po-file) finden Sie eine Erläuterung für das, was jeder Eintrag in diesem Beispiel darstellt.</span><span class="sxs-lookup"><span data-stu-id="ac328-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span> <span data-ttu-id="ac328-166">Hinzufügen einer Sprache mit verschiedenen Pluralformen</span><span class="sxs-lookup"><span data-stu-id="ac328-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="ac328-167">Im vorherigen Beispiel wurden englische und französische Zeichenfolgen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ac328-167">English and French strings were used in the previous example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="ac328-168">Englisch und Französisch haben nur zwei Pluralformen und teilen die gleichen Formregeln. Die Kardinalität der einen wird der ersten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ac328-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="ac328-169">Alle anderen Kardinalitäten werden der zweiten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ac328-169">Any other cardinality is mapped to the second plural form.</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="ac328-170">Nicht alle Sprachen verfügen über die gleichen Regeln.</span><span class="sxs-lookup"><span data-stu-id="ac328-170">Not all languages share the same rules.</span></span> <span data-ttu-id="ac328-171">Tschechisch verfügt beispielsweise über drei Pluralformen.</span><span class="sxs-lookup"><span data-stu-id="ac328-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="ac328-172">Erstellen Sie die `cs.po`-Datei wie folgt, und beachten Sie die drei verschiedenen Übersetzungen der Pluralformen:</span><span class="sxs-lookup"><span data-stu-id="ac328-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

<span data-ttu-id="ac328-173">Fügen Sie für die Annahme der tschechischen Lokalisierungen `"cs"` zur Liste der unterstützten Kulturen in der `ConfigureServices`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="ac328-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="ac328-174">Bearbeiten Sie die *Views/Home/About.cshtml*-Datei zum Rendern von lokalisierten Pluralzeichenfolgen für mehrere Kardinalitäten:</span><span class="sxs-lookup"><span data-stu-id="ac328-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="ac328-175">**Hinweis**: In einem echten Szenario würde eine Variable die Anzahl darstellen.</span><span class="sxs-lookup"><span data-stu-id="ac328-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="ac328-176">Hier wiederholen wir den gleichen Code mit drei verschiedenen Werten, um einen sehr speziellen Fall verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="ac328-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span> <span data-ttu-id="ac328-177">Beim Wechsel von Kulturen sehen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ac328-177">Upon switching cultures, you see the following:</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="ac328-178">Für `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="ac328-178">For `/Home/About`:</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="ac328-179">Für `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="ac328-179">For `/Home/About?culture=fr`:</span></span>

<span data-ttu-id="ac328-180">Für `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="ac328-180">For `/Home/About?culture=cs`:</span></span> <span data-ttu-id="ac328-181">Beachten Sie, dass die drei Übersetzungen für die tschechische Kultur unterschiedlich sind.</span><span class="sxs-lookup"><span data-stu-id="ac328-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="ac328-182">Die französische und englische Kultur teilen die gleiche Konstruktion für die beiden letzten übersetzten Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="ac328-182">The French and English cultures share the same construction for the two last translated strings.</span></span> <span data-ttu-id="ac328-183">Aufgaben für Fortgeschrittene</span><span class="sxs-lookup"><span data-stu-id="ac328-183">Advanced tasks</span></span>

<span data-ttu-id="ac328-184">Kontextualisieren von Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="ac328-184">Contextualizing strings</span></span> <span data-ttu-id="ac328-185">Anwendungen enthalten die zu übersetzenden Zeichenfolgen häufig an mehreren Stellen.</span><span class="sxs-lookup"><span data-stu-id="ac328-185">Applications often contain the strings to be translated in several places.</span></span>

<span data-ttu-id="ac328-186">Dieselbe Zeichenfolge kann möglicherweise über unterschiedliche Übersetzungen in bestimmten Speicherorten innerhalb einer Anwendung verfügen (Razor-Ansichten oder Klassendateien).</span><span class="sxs-lookup"><span data-stu-id="ac328-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="ac328-187">Eine PO-Datei unterstützt das Konzept eines Dateikontexts, der zum Kategorisieren von dargestellten Zeichenfolgen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ac328-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="ac328-188">Bei der Verwendung eines Dateikontexts kann eine Zeichenfolge unterschiedlich übersetzt werden. Dies hängt vom Dateikontext (oder einem fehlenden Dateikontext) ab.</span><span class="sxs-lookup"><span data-stu-id="ac328-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span> <span data-ttu-id="ac328-189">Die PO-Lokalisierungsdienste verwenden den Namen der vollständigen Klasse oder der Ansicht, die bei der Übersetzung einer Zeichenfolge verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ac328-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span>

<span data-ttu-id="ac328-190">Dies wird durch Festlegen des Werts im `msgctxt`-Eintrag erreicht.</span><span class="sxs-lookup"><span data-stu-id="ac328-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span> <span data-ttu-id="ac328-191">Ziehen Sie eine geringfügige Hinzufügung zum vorherigen *fr.po*-Beispiel in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="ac328-191">Consider a minor addition to the previous *fr.po* example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="ac328-192">Eine Razor-Ansicht in *Views/Home/About.cshtml* kann als Dateikontext definiert werden, indem der reservierte `msgctxt`-Eintragswert festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="ac328-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

<span data-ttu-id="ac328-193">Wenn `msgctxt` festgelegt ist, wird bei der Navigation zu `/Home/About?culture=fr-FR` eine Textübersetzung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ac328-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="ac328-194">Die Übersetzung wird nicht bei der Navigation zu `/Home/Contact?culture=fr-FR` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ac328-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="ac328-195">Wenn kein bestimmter Eintrag mit einem bestimmten Dateikontext abgeglichen wird, sucht der Fallbackmechanismus von Orchard Core nach einer entsprechenden PO-Datei ohne Kontext.</span><span class="sxs-lookup"><span data-stu-id="ac328-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span>

<span data-ttu-id="ac328-196">Vorausgesetzt, dass kein bestimmter Dateikontext für *Views/Home/Contact.cshtml* definiert ist, lädt eine Navigation zu `/Home/Contact?culture=fr-FR` eine PO-Datei, wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="ac328-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span> <span data-ttu-id="ac328-197">Ändern des Speicherorts für PO-Dateien</span><span class="sxs-lookup"><span data-stu-id="ac328-197">Changing the location of PO files</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="ac328-198">Der Standardspeicherort der PO-Dateien kann in `ConfigureServices` geändert werden:</span><span class="sxs-lookup"><span data-stu-id="ac328-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

<span data-ttu-id="ac328-199">In diesem Beispiel werden die PO-Dateien aus dem Ordner *Lokalisierung* geladen.</span><span class="sxs-lookup"><span data-stu-id="ac328-199">In this example, the PO files are loaded from the *Localization* folder.</span></span> <span data-ttu-id="ac328-200">Implementieren einer benutzerdefinierten Logik für die Suche nach Lokalisierungsdateien</span><span class="sxs-lookup"><span data-stu-id="ac328-200">Implementing a custom logic for finding localization files</span></span> <span data-ttu-id="ac328-201">Wenn eine komplexere Logik erforderlich ist, um die PO-Dateien zu finden, kann die `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`-Schnittstelle implementiert und als Dienst registriert werden.</span><span class="sxs-lookup"><span data-stu-id="ac328-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span>

<span data-ttu-id="ac328-202">Dies ist nützlich, wenn PO-Dateien an unterschiedlichen Speicherorten gespeichert oder die Dateien innerhalb einer Hierarchie von Ordnern gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="ac328-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>
