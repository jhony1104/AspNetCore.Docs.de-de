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
# <a name="configure-portable-object-localization-in-aspnet-core"></a>'Blazor'

'Identity'

'Let's Encrypt'

'Razor' 'SignalR' uid: fundamentals/portable-object-localization

Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core

## <a name="what-is-a-po-file"></a>Von [Sébastien Ros](https://github.com/sebastienros) und [Scott Addie](https://twitter.com/Scott_Addie)

Dieser Artikel erläutert die Schritte zur Verwendung von portablen Objektdateien (PO) in einer ASP.NET Core-Anwendung mit dem Framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore). **Hinweis**: Orchard Core ist kein Microsoft-Produkt.
- Microsoft bietet daher keinen Support für dieses Feature an.
- [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) Was ist eine PO-Datei?
- PO-Dateien werden als Textdateien verteilt, die übersetzte Zeichenfolgen für eine bestimmte Sprache enthalten.

### <a name="example"></a>Einige Vorteile der Verwendung von PO-Dateien im Vergleich zu *RESX*-Dateien sind:

PO-Dateien unterstützen Pluralisierung; *RESX*-Dateien unterstützen sie nicht.

PO-Dateien werden nicht wie *RESX*-Dateien kompiliert.

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

Daher sind spezialisierte Tools und die Buildschritte nicht erforderlich.

- PO-Dateien funktionieren gut mit gemeinsamen Onlinebearbeitungstools. Beispiel
- Hier sehen Sie eine PO-Beispieldatei, die die Übersetzung für zwei Zeichenfolgen in Französisch, einschließlich der Pluralform einer der Zeichenfolgen enthält:
- *fr.po*

In diesem Beispiel wird die folgende Syntax verwendet:

- `#:`: ein Kommentar, der den Kontext der zu übersetzenden Zeichenfolge angibt.
- Die gleiche Zeichenfolge wird möglicherweise anders übersetzt, je nachdem, wo sie verwendet wird.
- `msgid`: die nicht übersetzte Zeichenfolge.

`msgstr`: die übersetzte Zeichenfolge.

## <a name="configuring-po-file-support-in-aspnet-core"></a>Wenn die Pluralisierung unterstützt wird, können mehrere Einträge definiert werden.

`msgid_plural`: die nicht übersetzte Zeichenfolge im Plural.

### <a name="referencing-the-package"></a>`msgstr[0]`: die übersetzte Zeichenfolge für den Fall 0.

`msgstr[N]`: die übersetzte Zeichenfolge für den Fall N. Die PO-Dateispezifikation finden Sie [hier](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).

Konfigurieren der PO-Dateiunterstützung in ASP.NET Core

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Dieses Beispiel beruht auf einer ASP.NET Core MVC-Anwendung, die aus einer Visual Studio 2017-Projektvorlage generiert wurde.

Verweisen auf das Paket

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Fügen Sie einen Verweis auf das NuGet-Paket `OrchardCore.Localization.Core` hinzu.

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Dieses ist auf [MyGet](https://www.myget.org/) unter der Paketquelle https://www.myget.org/F/orchardcore-preview/api/v3/index.json verfügbar. Die *CSPROJ*-Datei enthält nun eine Zeile, die der folgenden Zeile ähnelt (die Versionsnummer kann davon abweichen):

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

Registrieren des Diensts

### <a name="creating-a-po-file"></a>Fügen Sie die erforderlichen Dienste zur `ConfigureServices`-Methode von *Startup.cs* hinzu:

Fügen Sie die erforderliche Middleware zur `Configure`-Methode von *Startup.cs* hinzu: Fügen Sie den folgenden Code zur Razor-Ansicht Ihrer Wahl hinzu.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

In diesem Beispiel wird *About.cshtml* verwendet. Eine `IViewLocalizer`-Instanz wird eingefügt und zum Übersetzen des Texts „Hello world!“ verwendet. Erstellen einer PO-Datei

### <a name="testing-the-application"></a>Erstellen Sie eine Datei mit dem Namen *\<culture code>.po* im Stammordner Ihrer Anwendung.

In diesem Beispiel lautet der Dateiname *fr.po*, weil die französische Sprache verwendet wird: Diese Datei speichert die zu übersetzende Zeichenfolge sowie die französische Zeichenfolge. Übersetzungen stellen ihre übergeordnete Kultur wieder her, wenn erforderlich.

In diesem Beispiel wird die *fr.po*-Datei verwendet, wenn die angeforderte Kultur `fr-FR` oder `fr-CA` ist. Testen der Anwendung Führen Sie Ihre Anwendung aus, und navigieren Sie zur URL`/Home/About`.

## <a name="pluralization"></a>Der Text **Hello world!**

wird angezeigt. Navigieren Sie zur URL `/Home/About?culture=fr-FR`.

Der Text **Bonjour le Monde!**

### <a name="creating-pluralization-po-files"></a>wird angezeigt.

Pluralisierung

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

PO-Dateien unterstützen Pluralformen. Dies ist nützlich, wenn die gleiche Zeichenfolge aufgrund einer Kardinalität unterschiedlich übersetzt werden muss.

### <a name="adding-a-language-using-different-pluralization-forms"></a>Diese Aufgabe ist komplizierter, da jede Sprache benutzerdefinierte Regeln festlegt, um auf der Grundlage von Kardinalität auszuwählen, welche Zeichenfolge zu verwenden ist.

Das Orchard Localization-Paket stellt eine API zur Verfügung, um diese anderen Pluralformen automatisch aufzurufen. Erstellen von PO-Pluraldateien Fügen Sie den folgenden Inhalt zur zuvor erwähnten *fr.po*-Datei hinzu:

Unter [Was ist eine PO-Datei?](#what-is-a-po-file) finden Sie eine Erläuterung für das, was jeder Eintrag in diesem Beispiel darstellt. Hinzufügen einer Sprache mit verschiedenen Pluralformen

Im vorherigen Beispiel wurden englische und französische Zeichenfolgen verwendet.

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

Englisch und Französisch haben nur zwei Pluralformen und teilen die gleichen Formregeln. Die Kardinalität der einen wird der ersten Pluralform zugeordnet.

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

Alle anderen Kardinalitäten werden der zweiten Pluralform zugeordnet.

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

Nicht alle Sprachen verfügen über die gleichen Regeln. Tschechisch verfügt beispielsweise über drei Pluralformen.

Erstellen Sie die `cs.po`-Datei wie folgt, und beachten Sie die drei verschiedenen Übersetzungen der Pluralformen:

Fügen Sie für die Annahme der tschechischen Lokalisierungen `"cs"` zur Liste der unterstützten Kulturen in der `ConfigureServices`-Methode hinzu:

```html
There is one item.
There are 2 items.
There are 5 items.
```

Bearbeiten Sie die *Views/Home/About.cshtml*-Datei zum Rendern von lokalisierten Pluralzeichenfolgen für mehrere Kardinalitäten:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

**Hinweis**: In einem echten Szenario würde eine Variable die Anzahl darstellen.

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Hier wiederholen wir den gleichen Code mit drei verschiedenen Werten, um einen sehr speziellen Fall verfügbar zu machen. Beim Wechsel von Kulturen sehen Sie Folgendes:

## <a name="advanced-tasks"></a>Für `/Home/About`:

### <a name="contextualizing-strings"></a>Für `/Home/About?culture=fr`:

Für `/Home/About?culture=cs`: Beachten Sie, dass die drei Übersetzungen für die tschechische Kultur unterschiedlich sind. Die französische und englische Kultur teilen die gleiche Konstruktion für die beiden letzten übersetzten Zeichenfolgen. Aufgaben für Fortgeschrittene

Kontextualisieren von Zeichenfolgen Anwendungen enthalten die zu übersetzenden Zeichenfolgen häufig an mehreren Stellen.

Dieselbe Zeichenfolge kann möglicherweise über unterschiedliche Übersetzungen in bestimmten Speicherorten innerhalb einer Anwendung verfügen (Razor-Ansichten oder Klassendateien). Eine PO-Datei unterstützt das Konzept eines Dateikontexts, der zum Kategorisieren von dargestellten Zeichenfolgen verwendet werden kann.

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Bei der Verwendung eines Dateikontexts kann eine Zeichenfolge unterschiedlich übersetzt werden. Dies hängt vom Dateikontext (oder einem fehlenden Dateikontext) ab. Die PO-Lokalisierungsdienste verwenden den Namen der vollständigen Klasse oder der Ansicht, die bei der Übersetzung einer Zeichenfolge verwendet wird.

Dies wird durch Festlegen des Werts im `msgctxt`-Eintrag erreicht. Ziehen Sie eine geringfügige Hinzufügung zum vorherigen *fr.po*-Beispiel in Erwägung.

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>Eine Razor-Ansicht in *Views/Home/About.cshtml* kann als Dateikontext definiert werden, indem der reservierte `msgctxt`-Eintragswert festgelegt wird:

Wenn `msgctxt` festgelegt ist, wird bei der Navigation zu `/Home/About?culture=fr-FR` eine Textübersetzung ausgeführt.

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Die Übersetzung wird nicht bei der Navigation zu `/Home/Contact?culture=fr-FR` ausgeführt.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Wenn kein bestimmter Eintrag mit einem bestimmten Dateikontext abgeglichen wird, sucht der Fallbackmechanismus von Orchard Core nach einer entsprechenden PO-Datei ohne Kontext.

Vorausgesetzt, dass kein bestimmter Dateikontext für *Views/Home/Contact.cshtml* definiert ist, lädt eine Navigation zu `/Home/Contact?culture=fr-FR` eine PO-Datei, wie beispielsweise: Ändern des Speicherorts für PO-Dateien

### <a name="using-a-different-default-pluralized-language"></a>Der Standardspeicherort der PO-Dateien kann in `ConfigureServices` geändert werden:

In diesem Beispiel werden die PO-Dateien aus dem Ordner *Lokalisierung* geladen. Implementieren einer benutzerdefinierten Logik für die Suche nach Lokalisierungsdateien Wenn eine komplexere Logik erforderlich ist, um die PO-Dateien zu finden, kann die `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`-Schnittstelle implementiert und als Dienst registriert werden.

Dies ist nützlich, wenn PO-Dateien an unterschiedlichen Speicherorten gespeichert oder die Dateien innerhalb einer Hierarchie von Ordnern gefunden werden können.
