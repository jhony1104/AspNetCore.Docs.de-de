---
title: Migrating from ASP.NET MVC to ASP.NET Core MVC (Migrieren von ASP.NET MVC zu ASP.NET Core MVC)
author: ardalis
description: Erfahren Sie, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu ASP.net Core MVC beginnen.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777045"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrating from ASP.NET MVC to ASP.NET Core MVC (Migrieren von ASP.NET MVC zu ASP.NET Core MVC)

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)und [Scott Addie](https://scottaddie.com)

In diesem Artikel wird beschrieben, wie Sie die Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](../mvc/overview.md)starten. In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben. Die Migration von ASP.NET MVC ist ein Prozess mit mehreren Schritten. dieser Artikel behandelt die anfängliche Einrichtung, die grundlegenden Controller und Sichten, statische Inhalte und Client seitige Abhängigkeiten. In weiteren Artikeln wird die Migration von Konfigurations-und Identitätscode in vielen ASP.NET MVC-Projekten behandelt.

> [!NOTE]
> Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell. Möglicherweise müssen Sie Ihre Projekte entsprechend aktualisieren.

## <a name="create-the-starter-aspnet-mvc-project"></a>Erstellen des MVC-Projekts Starter ASP.net

Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app. Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem ASP.net Core Projekt übereinstimmt, das im nächsten Schritt erstellt wird.

![Visual Studio - Dialogfeld „Neues Projekt“](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage im Bereich "ASP.net Templates"](mvc/_static/new-project-select-mvc-template.png)

*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*. Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.

## <a name="create-the-aspnet-core-project"></a>Erstellen des ASP.net Core Projekts

Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt (*"WebApp1"*), damit die Namespaces in den beiden Projekten übereinstimmen. Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren. Sie müssen dieses Projekt in einem anderen Verzeichnis als dem vorherigen Projekt erstellen, um denselben Namen zu verwenden.

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": leere Projektvorlage im Bereich "ASP.net Core Vorlagen" ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app. Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus. Benennen Sie diese APP in *fullaspnetcore*um. Wenn Sie dieses Projekt erstellen, sparen Sie Zeit bei der Konvertierung. Sie können sich den von der Vorlage generierten Code ansehen, um das Endergebnis anzuzeigen oder Code in das Konvertierungs Projekt zu kopieren. Es ist auch hilfreich, wenn Sie an einem Konvertierungs Schritt hängen bleiben, um mit dem Vorlagen generierten Projekt zu vergleichen.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurieren des Standorts für die Verwendung von MVC

::: moniker range=">= aspnetcore-2.1"

* Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen. Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden. Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket "Microsoft. aspnetcore. all](xref:fundamentals/metapackage) " verwiesen. Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden. Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Wenn .net Core oder .NET Framework als Ziel verwendet wird, werden Pakete, die häufig von MVC-Apps verwendet werden, einzeln in der Projektdatei aufgelistet.

::: moniker-end

`Microsoft.AspNetCore.Mvc`ist das ASP.net Core MVC-Framework. `Microsoft.AspNetCore.StaticFiles`ist der statische Datei Handler. Die ASP.net Core-Laufzeit ist modular aufgebaut, und Sie müssen sich explizit für die Bereitstellung statischer Dateien entscheiden (siehe [statische Dateien](xref:fundamentals/static-files)).

* Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

Die `UseStaticFiles` Erweiterungsmethode fügt den statischen Datei Handler hinzu. Wie bereits erwähnt, ist die ASP.NET-Laufzeit modular aufgebaut, und Sie müssen sich explizit für die Bereitstellung statischer Dateien entscheiden. Die `UseMvc` Erweiterungsmethode fügt Routing hinzu. Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.

## <a name="add-a-controller-and-view"></a>Controller und Ansicht hinzufügen

In diesem Abschnitt fügen Sie einen minimalen Controller und eine Ansicht hinzu, die als Platzhalter für den ASP.NET MVC-Controller und die Ansichten fungieren, die Sie im nächsten Abschnitt migrieren.

* Fügen Sie einen *Controller* Ordner hinzu.

* Fügen Sie dem Ordner *Controllers* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* Fügen Sie einen *views* -Ordner hinzu.

* Fügen Sie einen *views/Home-* Ordner hinzu.

* Fügen Sie dem Ordner *views/Home* eine ** Razor Ansicht** mit dem Namen *Index. cshtml* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

Die Projektstruktur wird unten dargestellt:

![Projektmappen-Explorer, die Dateien und Ordner von "WebApp1" anzeigt](mvc/_static/project-structure-controller-view.png)

Ersetzen Sie den Inhalt der Datei *views/Home/Index. cshtml* durch Folgendes:

```html
<h1>Hello world!</h1>
```

Führen Sie die App aus.

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview) .

Nachdem wir nun über ein minimales Funktions ASP.net Core Projekt verfügen, können wir mit der Migration von Funktionen aus dem ASP.NET MVC-Projekt beginnen. Wir müssen Folgendes verschieben:

* Client seitiger Inhalt (CSS, Schriftarten und Skripts)

* Controller

* views

* Modelle

* anbietet

* Filter

* Melden Sie sich Identity an/aus (Dies erfolgt im nächsten Tutorial).

## <a name="controllers-and-views"></a>Controller und Ansichten

* Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue. `HomeController` Beachten Sie, dass in ASP.NET MVC der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage " [Action result](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);" lautet. in ASP.net Core MVC wird stattdessen die Aktionsmethode `IActionResult` zurückgegeben. `ActionResult`implementiert `IActionResult`, sodass es nicht erforderlich ist, den Rückgabetyp ihrer Aktionsmethoden zu ändern.

* Kopieren Sie die Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* Razor " vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.

* Führen Sie die ASP.net Core-App aus, und testen Sie jede Methode. Wir haben die Layoutdatei oder Stile noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten. Die Layoutdatei generiert keine Verknüpfungen für die `About` Ansichten `Contact` und. Daher müssen Sie Sie über den Browser aufrufen (ersetzen Sie **4492** durch die Portnummer, die in Ihrem Projekt verwendet wird).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Kontaktseite](mvc/_static/contact-page.png)

Beachten Sie den Mangel an Formatierungs-und Menü Elementen. Dies soll im nächsten Abschnitt behoben werden.

## <a name="static-content"></a>Statischer Inhalt

In früheren Versionen von ASP.NET MVC wurde statischer Inhalt vom Stamm des Webprojekts gehostet und mit serverseitigen Dateien gemischt. In ASP.net Core wird statischer Inhalt im Ordner " *wwwroot* " gehostet. Sie möchten den statischen Inhalt aus ihrer alten ASP.NET MVC-app in den Ordner " *wwwroot* " in Ihrem ASP.net Core Projekt kopieren. In dieser Beispiel Konvertierung:

* Kopieren Sie die Datei *Favicon. ico* aus dem alten MVC-Projekt in den Ordner *wwwroot* im Projekt ASP.net Core.

Das alte ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Ordnern *Inhalt* und *Skripts* . Die Vorlage, die das alte ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*). Sie könnten die Dateien " *Bootstrap. js* " und " *Bootstrap. CSS* " aus dem ASP.NET-MVC-Projekt in den Ordner " *wwwroot* " im neuen Projekt kopieren. Stattdessen wird die Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt hinzugefügt.

## <a name="migrate-the-layout-file"></a>Migrieren der Layoutdatei

* Kopieren Sie die Datei *_ViewStart. cshtml* aus dem Ordner *views* des alten ASP.NET MVC-Projekts in den Ordner *views* des ASP.net Core Projekts. Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.

* Erstellen Sie einen *views/Shared-* Ordner.

* *Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Projekt Ansichts Ordner des *fullaspnetcore* -MVC-Projekts in *den Ordner* *views* des ASP.net Core Projekts. Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* . Die Datei *_ViewImports. cshtml* enthält Namespaces für alle Ansichts Dateien und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus. Taghilfsprogramme werden in der neuen Layoutdatei verwendet. Die Datei *_ViewImports. cshtml* ist neu für ASP.net Core.

* Kopieren Sie die Datei " *_Layout. cshtml* " aus dem alten *ASP.net des alten MVC-Projekts* in die Ansichten/den frei *gegebenen* Ordner des ASP.net Core Projekts.

Öffnen Sie *_Layout. cshtml* -Datei, und nehmen Sie die folgenden Änderungen vor (der abgeschlossene Code wird unten angezeigt):

* Ersetzen `@Styles.Render("~/Content/css")` Sie dies `<link>` durch ein-Element, das *Bootstrap. CSS* laden soll (siehe unten).

* Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.

* Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie `@*...*@`die Zeile mit). Weitere Informationen finden [Sie unter Migrieren Identity der Authentifizierung und zu ASP.net Core](xref:migration/identity)

* Ersetzen `@Scripts.Render("~/bundles/jquery")` Sie dies `<script>` durch ein-Element (siehe unten).

* Ersetzen `@Scripts.Render("~/bundles/bootstrap")` Sie dies `<script>` durch ein-Element (siehe unten).

Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zeigen Sie die Website im Browser an. Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.

* *Optional:* Möglicherweise möchten Sie versuchen, die neue Layoutdatei zu verwenden. Für dieses Projekt können Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt kopieren. Die neue Layoutdatei verwendet [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und bietet weitere Verbesserungen.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Beheben von HTTP 500-Fehlern

Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält. Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der nicht in Ihrem Projekt vorhanden ist, erhalten Sie einen HTTP 500-Fehler. Standardmäßig wird die `UseDeveloperExceptionPage` Erweiterung in ASP.net Core-apps dem `IApplicationBuilder` hinzugefügt und ausgeführt, wenn die Konfiguration *entwickelt*wird. Dies wird im folgenden Code ausführlich erläutert:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.net Core konvertiert nicht behandelte Ausnahmen in einer Web-App in HTTP 500-Fehler Antworten. Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern. Weitere Informationen finden **Sie unter Verwenden der Seite "Entwickler Ausnahme** " unter [Behandeln von Fehlern](../fundamentals/error-handling.md) .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
