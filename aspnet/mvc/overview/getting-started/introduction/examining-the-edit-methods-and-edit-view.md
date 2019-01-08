---
uid: mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
title: Überprüfen der Edit-Methoden und-Ansicht | Microsoft-Dokumentation
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 52a4d5fe-aa31-4471-b3cb-a064f82cb791
msc.legacyurl: /mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
msc.type: authoredcontent
ms.openlocfilehash: 75fd3a7dd55107cbdb9095d5b54b616133b4f65e
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099395"
---
<a name="examining-the-edit-methods-and-edit-view"></a>Überprüfen der Edit-Methoden und-Ansicht
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

In diesem Abschnitt werden Sie überprüfen die generierte `Edit` Aktionsmethoden und Ansichten für die Movie-Controller. Aber zunächst wird eine kurze dieses Rätsel, um das Datum der Veröffentlichung Aussehen des Suchsteuerelements ansprechender machen. Öffnen der *Models\Movie.cs* Datei, und fügen Sie die unten gezeigten markierten Zeilen hinzu:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample1.cs?highlight=2,12-14)]

Sie können auch die Datumskultur bestimmte wie folgt erstellen:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample2.cs?highlight=3)]

Wir behandeln [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) im nächsten Tutorial. Das [Display](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“). Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) -Attribut gibt den Typ der Daten, die in diesem Fall ist es ein Datum ist, damit die im Feld gespeicherten Uhrzeitinformationen nicht angezeigt wird. Die [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) Attribut ist erforderlich, für einen Fehler im Chrome-Browser, die Datums-und Uhrzeitformate falsch rendert.

Führen Sie die Anwendung, und navigieren Sie zu der `Movies` Controller. Halten Sie den Mauszeiger über ein **bearbeiten** Link, um die URL anzuzeigen, die es verknüpft.

![EditLink_sm](examining-the-edit-methods-and-edit-view/_static/image1.png)

Die **bearbeiten** Link wurde generiert, indem die `Html.ActionLink` -Methode in der die *Views\Movies\Index.cshtml* anzeigen:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample3.cshtml)]

![Html.ActionLink](examining-the-edit-methods-and-edit-view/_static/image2.png)

Die `Html` Objekt ist eine Hilfsklasse, die verfügbar, die mithilfe einer Eigenschaft gemacht wird für die [System.Web.Mvc.WebViewPage](https://msdn.microsoft.com/library/gg402107(VS.98).aspx) Basisklasse. Die `ActionLink` Methode des Hilfsprogramms erleichtert das HTML-Links dynamisch zu generieren, die auf Aktionsmethoden in Controllern verknüpft sind. Das erste Argument für die `ActionLink` Methode ist der Text des Links zum Rendern (z. B. `<a>Edit Me</a>`). Das zweite Argument ist der Name der aufzurufenden Aktionsmethode (In diesem Fall die `Edit` Aktion). Das letzte Argument ist ein [anonymes Objekt](https://weblogs.asp.net/scottgu/archive/2007/05/15/new-orcas-language-feature-anonymous-types.aspx) , generiert die Routendaten (in diesem Fall wird die ID von 4).

Der generierte Link in der vorherigen Abbildung dargestellt ist `http://localhost:1234/Movies/Edit/4`. Die Standardroute (in eingestellten *App\_start\routeconfig*) nimmt das URL-Muster `{controller}/{action}/{id}`. Aus diesem Grund ASP.NET übersetzt `http://localhost:1234/Movies/Edit/4` in eine Anforderung an die `Edit` Action-Methode der der `Movies` Controller mit dem Parameter `ID` gleich 4. Überprüfen Sie den folgenden Code aus der *App\_start\routeconfig* Datei. Die [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) Methode dient zum Weiterleiten von HTTP-Anforderungen an die richtige Methode für Controller und eine Aktion aus, und geben Sie die optionale ID-Parameter. Die [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) Methode wird auch verwendet, durch die [HtmlHelpers](https://msdn.microsoft.com/library/system.web.mvc.htmlhelper(v=vs.108).aspx) wie z. B. `ActionLink` zum Generieren von URLs, die angegeben wird, den Controller, Action-Methode und alle Routendaten.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample4.cs?highlight=7)]

Sie können auch die Aktionsmethodenparameter, die mithilfe einer Abfragezeichenfolge übergeben. Z. B. die URL `http://localhost:1234/Movies/Edit?ID=3` übergibt außerdem den Parameter `ID` von 3 aus, um die `Edit` Action-Methode der der `Movies` Controller.

![EditQueryString](examining-the-edit-methods-and-edit-view/_static/image3.png)

Öffnen der `Movies` Controller. Die beiden `Edit` Aktionsmethoden werden unten angezeigt.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample5.cs?highlight=19-21)]

Beachten Sie, dass der zweiten `Edit`-Aktionsmethode das `HttpPost`-Attribut vorangestellt ist. Dieses Attribut gibt an, dass die Überladung von der `Edit` Methode kann nur für die POST-Anforderungen aufgerufen werden. Sie können Anwenden der `HttpGet` -Attribut auf die erste Bearbeitungsmethode, aber dies ist nicht erforderlich, da dies die Standardeinstellung ist. (Wir werden den finden Sie in Aktionsmethoden, die implizit zugewiesen sind die `HttpGet` als Attribut `HttpGet` Methoden.) Die [binden](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) -Attribut ist eine andere wichtige Sicherheitsmechanismus, der verhindert, dass Hacker overposting-Daten an Ihr Modell. Sie sollten nur Eigenschaften in der Bind-Attribut enthalten, die Sie ändern möchten. Informieren Sie sich über Overposting und das Attribut "Bind" in meinem [overposting Sicherheitshinweis](https://go.microsoft.com/fwlink/?LinkId=317598). In dem einfachen Modell, die in diesem Tutorial verwendet wird werden wir alle Daten in das Modell binden, werden. Die [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) -Attribut wird verwendet, um die Fälschung einer Anforderung zu verhindern und ist mit-Rückrufvertrag `@Html.AntiForgeryToken()` in der Datei für die Bearbeitungsansicht (*Views\Movies\Edit.cshtml*), ein Teil ist unten dargestellt:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample6.cshtml?highlight=9)]

`@Html.AntiForgeryToken()` generiert ein Token für ausgeblendeten Feld fälschungssicherheitstoken zur Verfügung, die in übereinstimmen müssen die `Edit` Methode der `Movies` Controller. Weitere Informationen zu websiteübergreifenden anforderungsfälschung (auch bekannt als XSRF oder CSRF) in meinem Lernprogramm [XSRF/CSRF-Schutz in MVC](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md).

Die `HttpGet` `Edit` Methode akzeptiert den Film-ID-Parameter, sucht den Film mithilfe von Entity Framework `Find` -Methode, und gibt den ausgewählten Film an die Bearbeitungsansicht zurück. Wenn ein Film nicht gefunden wird, [HttpNotFound](https://msdn.microsoft.com/library/gg453938(VS.98).aspx) zurückgegeben wird. Als das Gerüstsystem die Bearbeitungsansicht erstellt hat, wurde die `Movie`-Klasse überprüft und Code zum Rendern der `<label>`- und `<input>`-Elemente für jede Eigenschaft der Klasse erstellt. Das folgende Beispiel zeigt die Bearbeitungsansicht, die von der visual Studio-gerüstsystem generiert wurde:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample7.cshtml)]

Beachten Sie, wie die ansichtsvorlage verfügt über eine `@model MvcMovie.Models.Movie` -Anweisung am Anfang der Datei – gibt an, dass die Ansicht erwartet, das Modell für die ansichtsvorlage dass Typ `Movie`.

Der Gerüstcode verwendet mehrere *Hilfsmethoden* , das HTML-Markup zu optimieren. Die [ `Html.LabelFor` ](https://msdn.microsoft.com/library/gg401864(VS.98).aspx) Hilfsprogramm zeigt den Namen des Felds (&quot;Titel&quot;, &quot;ReleaseDate&quot;, &quot;"Genre"&quot;, oder &quot;Preis &quot;). Die [ `Html.EditorFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.editorextensions.editorfor(VS.98).aspx) -Hilfsmethode rendert eine HTML `<input>` Element. Die [ `Html.ValidationMessageFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.validationextensions.validationmessagefor(VS.98).aspx) Hilfsprogramm zeigt alle validierungsnachrichten dieser Eigenschaft zugeordnet.

Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL. Klicken Sie auf einen Link **Bearbeiten**. Zeigen Sie im Browser den Quelltext für die Seite an. HTML-Code für das Formularelement ist unten dargestellt.

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample8.cshtml?highlight=1-2)]

Die `<input>` Elemente sind in einem HTML- `<form>` Element, dessen `action` -Attribut festgelegt ist, zum an den */Filme/bearbeiten* URL. Die Formulardaten werden an den Server zurückgesendet werden bei der **speichern** geklickt wird. Die zweite Zeile zeigt das ausgeblendete [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) -Token von generiert die `@Html.AntiForgeryToken()` aufrufen.

## <a name="processing-the-post-request"></a>Verarbeiten der POST-Anforderung

Die folgende Liste zeigt die `HttpPost`-Version der `Edit`-Aktionsmethode.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample9.cs)]

Die [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) Attribut überprüft die [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) -Token von generiert die `@Html.AntiForgeryToken()` rufen Sie in der Ansicht.

Die [ASP.NET MVC-modellbindung](https://msdn.microsoft.com/library/dd410405.aspx) verwendet die übermittelten Formularwerte und erstellt eine `Movie` -Objekt, das als übergeben wird die `movie` Parameter. Die `ModelState.IsValid` stellt sicher, dass die im Formular übermittelten Daten verwendet werden können, ändern (bearbeiten oder aktualisieren) eine `Movie` Objekt. Wenn die Daten gültig ist, werden die Filmdaten gespeichert, auf die `Movies` Auflistung von der `db`(`MovieDBContext` Instanz). Die neuen Daten in der Datenbank gespeichert ist, durch den Aufruf der `SaveChanges` -Methode der `MovieDBContext`. Nach dem Speichern der Daten leitet der Code den Benutzer an die `Index`-Aktionsmethode der `MoviesController`-Klasse weiter, die die Filmsammlung einschließlich der gerade vorgenommenen Änderungen anzeigt.

Sobald die clientseitige Validierung, dass der Wert eines Felds nicht gültig ist feststellt, wird eine Fehlermeldung angezeigt. Wenn JavaScript deaktiviert ist, ist die clientseitige Validierung deaktiviert. Der Server erkennt jedoch die bereitgestellten Werte sind ungültig, und die Formularwerte werden wieder mit Fehlermeldungen angezeigt.

Überprüfung wird später in diesem Tutorial im Detail untersucht.

Die `Html.ValidationMessageFor` -Hilfsprogramme in den *Edit.cshtml* anzeigen, die Vorlage zum Anzeigen von entsprechende Fehlermeldungen kümmern.

![abcNotValid](examining-the-edit-methods-and-edit-view/_static/image4.png)

Alle der `HttpGet` Methoden folgen einem ähnlichen Muster. Sie erhalten ein Movie-Objekt (oder eine Liste von Objekten, die im Fall von `Index`), und übergeben Sie das Modell an die Ansicht. Die `Create` Methode übergibt ein leeres Movie-Objekt, an der Ansicht "erstellen". Alle Methoden, die Daten erstellen, bearbeiten, löschen oder in beliebiger Weise ändern, nutzen dazu die `HttpPost`-Überladung der Methode. Ändern von Daten in eine HTTP GET-Methode ein Sicherheitsrisiko dar, ist, wie in der Post-Blogeintrag beschrieben [ASP.NET MVC Tipp #46 – verwenden Sie keine Links löschen, da Sicherheitslücken entstehen](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx). Ändern von Daten in eine GET-Methode verstößt auch gegen bewährte HTTP-Methoden und das architektonische [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer) Muster, das angibt, dass GET-Anforderungen nicht den Status Ihrer Anwendung ändern sollten. Die Durchführung eines GET-Vorgangs sollte also eine sichere Operation sein, die keine Nebenwirkungen hat und die permanenten Daten nicht ändert.

## <a name="jquery-validation-for-non-english-locales"></a>jQuery-Validierung für nicht englischen Gebietsschemas

Wenn Sie einen Computer für Englisch (USA) verwenden, können Sie diesen Abschnitt überspringen und fahren Sie mit dem nächsten Tutorial. Sie können die Version dieses Tutorials Globalize [hier](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=16475). Ein ausgezeichnetes zweiteiligen-Tutorial, das an der Internationalisierung, finden Sie unter [Nadeems ASP.NET MVC 5-Internationalisierung](http://afana.me/post/aspnet-mvc-internationalization.aspx).


> [!NOTE]
> zur Unterstützung von jQuery-Validierung für nicht englische Gebietsschemas, in denen ein Komma (&quot;,&quot;) für ein Dezimaltrennzeichen und einem nicht-US-englischen Datums-und Uhrzeitformate, müssen Sie enthalten *globalize.js* und Ihren speziellen  *Cultures/globalize.Cultures.js* Datei (aus [ https://github.com/jquery/globalize ](https://github.com/jquery/globalize) ) und JavaScript verwenden `Globalize.parseFloat`. Sie können die jQuery-Validierung nicht englischen von NuGet abrufen. (Installieren Sie nicht Globalize bei Verwendung von einem englischen Gebietsschema.)

1. Aus der **Tools** Menü auf **NuGet Paket-Manager**, und klicken Sie dann auf **NuGet-Pakete verwalten, Lösung**.

    ![](examining-the-edit-methods-and-edit-view/_static/image5.png)
2. Wählen Sie im linken Bereich <strong>Durchsuchen *.</strong>* (Siehe Abbildung unten).
3. Geben Sie in das Eingabefeld, * Globalize **.

    ![](examining-the-edit-methods-and-edit-view/_static/image6.png) Wählen Sie `jQuery.Validation.Globalize`, wählen Sie `MvcMovie` , und klicken Sie auf **installieren**. Die *Scripts\jquery.globalize\globalize.js* Datei wird dem Projekt hinzugefügt werden. Die *Scripts\jquery.globalize\cultures\* Ordner enthält viele Kultur JavaScript-Dateien. Beachten Sie, dass sie dieses Paket installieren fünf Minuten dauern kann.

   Der folgende Code zeigt die Änderungen an der Datei Views\Movies\Edit.cshtml:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample10.cshtml)]

Um zu vermeiden, wiederholen diesen Code in jeder Ansicht bearbeiten, können Sie es in der Layoutdatei verschieben. Zur Optimierung des Skript-Downloads finden Sie in meinem Tutorial [Bündelung und Minimierung](../../performance/bundling-and-minification.md).

Weitere Informationen finden Sie unter [ASP.NET MVC 3-Internationalisierung](http://afana.me/post/aspnet-mvc-internationalization.aspx) und [ASP.NET MVC 3-Internationalisierung – Part 2 (NerdDinner)](http://afana.me/post/aspnet-mvc-internationalization-part-2.aspx).

Als eine vorübergehende Lösung Wenn Sie Überprüfung arbeiten in Ihrem Gebietsschema nicht, Sie können erzwingen, dass Ihr Computer mit Englisch (USA) oder können Sie JavaScript in Ihrem Browser deaktivieren. Sie können der Globalization-Element in das Stammverzeichnis für Projekte hinzufügen, um den Computer mit Englisch (USA) zu erzwingen, *"Web.config"* Datei. Der folgende Code zeigt die Globalization-Element mit der Kultur Englisch (USA) festgelegt.

[!code-xml[Main](examining-the-edit-methods-and-edit-view/samples/sample11.xml)]

<a id="gettingstarted"></a><a id="jQueryAjaxJSON"></a> Im nächsten Tutorial werden wir Suchfunktionen implementieren.

> [!div class="step-by-step"]
> [Zurück](accessing-your-models-data-from-a-controller.md)
> [Weiter](adding-search.md)
