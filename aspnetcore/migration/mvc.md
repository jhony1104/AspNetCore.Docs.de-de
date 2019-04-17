---
title: Migrieren von ASP.NET MVC zu ASP.NET Core MVC
author: ardalis
description: Informationen Sie zum Migrieren eines ASP.NET MVC-Projekts zu ASP.NET Core MVC beginnen.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a9e2b41b933ed04a23515564892ed1694a4ac4f8
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614395"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrieren von ASP.NET MVC zu ASP.NET Core MVC

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), und [Scott Addie](https://scottaddie.com)

In diesem Artikel zeigt, wie für den Einstieg in das Migrieren eines ASP.NET MVC-Projekts zu [ASP.NET Core MVC](../mvc/overview.md). Im Prozess wird hervorgehoben viele Funktionen, die von ASP.NET MVC geändert haben. Migrieren von ASP.NET MVC ist ein mehrstufiger Prozess, und dieser Artikel behandelt die ersteinrichtung, einfachen Controller und Ansichten, statischen Inhalt und clientseitige Abhängigkeiten. Zusätzliche Artikel decken Migrieren von Konfiguration und Code für die Identität in ASP.NET MVC-Projekte, die viele gefunden.

> [!NOTE]
> Die Versionsnummern in den Beispielen möglicherweise keine aktuellen. Sie müssen Ihre Projekte entsprechend aktualisieren.

## <a name="create-the-starter-aspnet-mvc-project"></a>Beim Start von ASP.NET MVC-Projekt erstellen

Um das Upgrade zu demonstrieren, beginnen wir durch das Erstellen einer ASP.NET MVC-app. Erstellen sie mit dem Namen *"WebApp1"* damit der Namespace des ASP.NET Core-Projekts entspricht, wir im nächsten Schritt erstellen.

![Visual Studio-Projekt-Dialogfeld](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage, die im Bereich von ASP.NET Vorlagen ausgewählt](mvc/_static/new-project-select-mvc-template.png)

*Optional:* Ändern Sie den Namen der Lösung von *"WebApp1"* zu *Mvc5*. Visual Studio zeigt den Namen der neuen Projektmappe (*Mvc5*), die erleichtert es, teilen Sie dieses Projekt aus das nächste Projekt.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core-Projekt erstellen

Erstellen Sie ein neues *leere* ASP.NET Core-Web-app mit den gleichen Namen wie das vorherige Projekt (*"WebApp1"*) damit die Namespaces in beiden Projekten übereinstimmen. Mit den gleichen Namespace erleichtert es, Code zwischen den beiden Projekten kopieren. Sie müssen zum Erstellen des Projekts in einem anderen Verzeichnis als das vorherige Projekt, das den gleichen Namen verwenden.

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Web Application": Leere Projektvorlage, die im Bereich von ASP.NET Core-Vorlagen ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Optional:* Erstellen Sie eine neue ASP.NET Core-app, mit der *Webanwendung* Projektvorlage. Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungsoption "des **einzelne Benutzerkonten**. Benennen Sie diese app *FullAspNetCore*. Erstellen dieses Projekt sparen Sie Zeit bei der Konvertierung. Sie können der Vorlage generierten Codes das Endergebnis oder Code in das Konvertierungsprojekt zu kopieren, ansehen. Es ist auch hilfreich, wenn Sie auf einen Konvertierungsschritt Vergleich mit der Vorlage generierte Projekt hängen bleiben.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurieren Sie den Standort für die Verwendung von MVC

::: moniker range=">= aspnetcore-2.1"

* Wenn .NET Core als Ziel der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) wird standardmäßig verwiesen. Dieses Paket enthält Pakete, die häufig verwendete Pakete von MVC-apps. Wenn .NET Framework abzielt, müssen die Paketverweise einzeln in der Projektdatei aufgeführt sein.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Wenn .NET Core als Ziel der [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) wird standardmäßig verwiesen. Dieses Paket enthält Pakete, die häufig verwendete Pakete von MVC-apps. Wenn .NET Framework abzielt, müssen die Paketverweise einzeln in der Projektdatei aufgeführt sein.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Wenn .NET Core oder .NET Framework abgezielt wird, werden Pakete, die häufig verwendete Pakete von MVC-apps in der Projektdatei einzeln aufgeführt.

::: moniker-end

`Microsoft.AspNetCore.Mvc` ist das ASP.NET Core MVC-Framework. `Microsoft.AspNetCore.StaticFiles` ist der Handler statische Dateien. Die ASP.NET Core-Runtime ist modular aufgebaut, und Sie müssen ausdrücklich zum Bereitstellen statischer Dateien (finden Sie unter [statische Dateien](xref:fundamentals/static-files)).

* Öffnen der *"Startup.cs"* Datei, und ändern Sie den Code entsprechend der folgenden:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

Die `UseStaticFiles` -Erweiterungsmethode fügt den Handler statische Dateien hinzu. Wie bereits erwähnt, ist die ASP.NET-Laufzeit modular aufgebaut, und Sie müssen ausdrücklich zum Bereitstellen statischer Dateien. Die `UseMvc` Erweiterungsmethode fügt routing. Weitere Informationen finden Sie unter [Anwendungsstart](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Fügen Sie einen Controller und Ansicht

In diesem Abschnitt fügen Sie einen minimalen Controller und eine Ansicht als Platzhalter für den ASP.NET MVC-Controller und Ansichten, dass Sie im nächsten Abschnitt migrieren müssen.

* Hinzufügen einer *Controller* Ordner.

* Hinzufügen einer **"Controller"-Klasse** mit dem Namen *"HomeController.cs"* auf die *Controller* Ordner.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* Hinzufügen einer *Ansichten* Ordner.

* Hinzufügen einer *Views/Home* Ordner.

* Hinzufügen einer **Razor-Ansicht** mit dem Namen *"Index.cshtml"* auf die *Views/Home* Ordner.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

Die Projektstruktur ist unten dargestellt:

![Projektmappen-Explorer mit Dateien und Ordner von "WebApp1"](mvc/_static/project-structure-controller-view.png)

Ersetzen Sie den Inhalt von der *Views/Home/Index.cshtml* Datei durch Folgendes:

```html
<h1>Hello world!</h1>
```

Führen Sie die App aus.

![Web-app in Microsoft Edge öffnen](mvc/_static/hello-world.png)

Finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview) für Weitere Informationen.

Nun, da wir ein minimale funktionierende ASP.NET Core-Projekt haben, können wir beginnen, migrieren Funktionen von ASP.NET MVC-Projekt. Wir müssen die folgenden verschieben:

* Client-Side-Inhalt (CSS, Schriftarten und Skripts)

* Controller

* Ansichten

* Modelle

* Bündeln

* Filter

* Melden Sie sich in/out "," Identity (Dies erfolgt im nächsten Tutorial.)

## <a name="controllers-and-views"></a>Controller und Ansichten

* Kopieren Sie jede der Methoden aus der ASP.NET MVC `HomeController` mit dem neuen `HomeController`. Beachten Sie, dass in ASP.NET MVC, der integrierten Vorlage Controller Aktion Methodenrückgabetyp [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, die Aktionsmethoden return `IActionResult` stattdessen. `ActionResult` implementiert `IActionResult`, daher keine Notwendigkeit besteht, den Rückgabetyp der Ihre Aktionsmethoden zu ändern.

* Kopieren der *"About.cshtml"*, *"Contact.cshtml"*, und *"Index.cshtml"* Razor-Ansichtsdateien aus dem ASP.NET MVC-Projekt auf ASP.NET Core-Projekt.

* Führen Sie die ASP.NET Core-app, und Testen Sie jede Methode. Wir noch nicht die Layoutdatei oder Stile noch migriert. daher die gerenderten Ansichten nur den Inhalt in die Ansichtsdateien enthalten. Sie müssen also nicht die Layout-Datei generiert, Links, um die `About` und `Contact` anzeigt, daher Sie sie aus dem Browser aufzurufen müssen (ersetzen Sie **4492** mit der Portnummer, die in Ihrem Projekt verwendet).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Seite "Kontakt"](mvc/_static/contact-page.png)

Beachten Sie, dass der Stil und Menüelementen. Dies soll im nächsten Abschnitt behoben werden.

## <a name="static-content"></a>Statischer Inhalt

In früheren Versionen von ASP.NET MVC statischer Inhalt wurde vom Stamm des Webprojekts gehostet und wurde mit einem serverseitigen Dateien vermischt. In ASP.NET Core, statischer Inhalt gehostet wird, der *"Wwwroot"* Ordner. Sie sollten den statischen Inhalt Ihre alten ASP.NET MVC-App zum Kopieren der *"Wwwroot"* Ordner in Ihrem ASP.NET Core-Projekt. Bei dieser Konvertierung Beispiel:

* Kopieren der *favicon.ico* -Datei aus dem alten MVC-Projekt in der *"Wwwroot"* Ordner im ASP.NET Core-Projekt.

Die alte ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für die Formatierung und speichert, Dateien mit der Bootstrap-Stil, der *Content* und *Skripts* Ordner. Die Vorlage, die mit das alte ASP.NET MVC-Projekt generiert wird, verweist auf Bootstrap in der Layoutdatei (*Views/Shared/_Layout.cshtml*). Sie können Kopieren der *"Bootstrap.js"* und *Datei "Bootstrap.CSS"* Dateien von ASP.NET MVC-Projekt auf die *"Wwwroot"* Ordner in das neue Projekt. Stattdessen fügen wir Unterstützung für den Bootstrap-Stil (und andere clientseitige Bibliotheken) mithilfe des CDNs im nächsten Abschnitt.

## <a name="migrate-the-layout-file"></a>Migrieren Sie die Layoutdatei

* Kopieren der *_ViewStart.cshtml* Datei aus des alten ASP.NET MVC-Projekts *Ansichten* Ordner in des ASP.NET Core-Projekts *Ansichten* Ordner. Die *_ViewStart.cshtml* Datei wurde in ASP.NET Core MVC nicht geändert.

* Erstellen Sie eine *Views/Shared* Ordner.

* *Optional:* Kopie *_ViewImports.cshtml* aus der *FullAspNetCore* MVC-Projekt *Ansichten* Ordner in des ASP.NET Core-Projekts *Ansichten* Ordner. Entfernen Sie alle Namespacedeklaration in der *_ViewImports.cshtml* Datei. Die *_ViewImports.cshtml* Datei stellt die Namespaces für alle Ansichtsdateien bereit und verbindet [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro). Taghilfsprogramme werden in die Neue Layoutdatei verwendet. Die *_ViewImports.cshtml* Datei ist neu in ASP.NET Core.

* Kopieren der *"_Layout.cshtml"* Datei aus des alten ASP.NET MVC-Projekts *Views/Shared* Ordner in des ASP.NET Core-Projekts *Views/Shared* Ordner.

Open *"_Layout.cshtml"* Datei, und stellen Sie die folgenden Änderungen vor (der vollständige Code wird unten dargestellt):

* Ersetzen Sie dies `@Styles.Render("~/Content/css")` mit einem `<link>` Element geladen *Datei "Bootstrap.CSS"* (siehe unten).

* Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.

* Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile (setzen Sie die Zeile mit `@*...*@`). Weitere Informationen finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core](xref:migration/identity)

* Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` mit einem `<script>` Element (siehe unten).

* Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` mit einem `<script>` Element (siehe unten).

Das Ersatz-Markup für die Aufnahme der Bootstrap-CSS:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Das Ersatz-Markup für jQuery und Bootstrap-JavaScript-einschließen:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Die aktualisierte *"_Layout.cshtml"* Datei ist unten dargestellt:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Die Website im Browser anzeigen. Es sollte jetzt ordnungsgemäß mit den erwarteten Formatvorlagen direktes Laden.

* *Optional:* Sie möchten möglicherweise versuchen Sie es mit der neuen Layoutdatei. Für dieses Projekt kopieren Sie die Layoutdatei "aus der *FullAspNetCore* Projekt. Die Neue Layoutdatei verwendet [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und weitere Verbesserungen.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

Informationen zum Konfigurieren der Bündelung und Minimierung, finden Sie unter [Bündelung und Minimierung](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Beheben von HTTP 500-Fehler

Es gibt viele Probleme, die eine HTTP 500 Fehlermeldung führen können, die keine Informationen für die Quelle des Problems enthalten. Z. B. wenn die *Views/_viewimports.cshtml* -Datei enthält einen Namespace, der in Ihrem Projekt nicht vorhanden ist, erhalten Sie einen HTTP 500-Fehler. Standardmäßig in ASP.NET Core-apps die `UseDeveloperExceptionPage` Erweiterung hinzugefügt wird die `IApplicationBuilder` und ausgeführt, wenn die Konfiguration ist *Entwicklung*. Der folgende Code veranschaulicht dies:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core konvertiert nicht behandelte Ausnahmen in einer Web-app in HTTP 500-Fehlerantworten. Fehlerdetails werden nicht in der Regel in diese Antworten Offenlegung potenziell vertraulicher Informationen über den Server enthalten. Finden Sie unter **mithilfe der Ausnahmeseite** in [Fehlerbehandlung](../fundamentals/error-handling.md) für Weitere Informationen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
