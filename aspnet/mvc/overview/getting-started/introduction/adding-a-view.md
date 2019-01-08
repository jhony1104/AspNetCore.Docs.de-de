---
title: Hinzufügen einer Ansicht zu einer MVC-app
author: Rick-Anderson
description: Hinzufügen einer Ansicht zu einer MVC-app
ms.author: riande
ms.date: 09/1721/2017
uid: mvc/overview/getting-started/introduction/adding-a-view
ms.openlocfilehash: 47447c82506cc0eb4dafabe272b3204f76a2edd7
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098661"
---
<a name="adding-a-view"></a>Hinzufügen einer Ansicht
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

In diesem Abschnitt also ändern Sie die `HelloWorldController` anzeigen, die Vorlagendateien an sauber zu kapseln den Prozess der Generierung von HTML-Antworten an einen Client zu verwendende Klasse an. 

Erstellen Sie eine Ansicht Vorlage mithilfe der [Razor-ansichtsengine](../../../../web-pages/overview/getting-started/introducing-razor-syntax-c.md). Razor-basierte Ansichtsvorlagen haben eine *.cshtml* Dateierweiterung aus, und geben Sie eine elegante Methode zum Erstellen von HTML-Ausgabe mithilfe von c#. Razor minimiert die Anzahl von Zeichen und Tastaturanschläge erforderlich, wenn eine ansichtsvorlage zu schreiben und ermöglicht eine schnelle, fließende Workflows programmieren.

Derzeit gibt die `Index`-Methode eine Zeichenfolge mit der Meldung zurück, die in der Controllerklasse hartcodiert ist. Ändern der `Index` Controller aufzurufende Methode [Ansicht](/dotnet/api/microsoft.aspnetcore.mvc.controller.view#Microsoft_AspNetCore_Mvc_Controller_View) Methode, wie im folgenden Code gezeigt:

[!code-csharp[Main](adding-a-view/samples/sample1.cs?highlight=1,3)]

Die `Index` oben genannte Methode verwendet eine ansichtsvorlage zum Generieren einer HTML-Antwortinhalts an den Browser. Controllermethoden (auch bekannt als [Aktionsmethoden](http://rachelappel.com/asp.net-mvc-actionresults-explained)), wie z. B. die `Index` oben in der Regel Methodenrückgabewert ein [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx) (oder eine abgeleitete Klasse [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx)), keine primitiven Typen wie Zeichenfolge.

Klicken Sie mit der rechten Maustaste auf die *Views\HelloWorld* Ordner, und klicken Sie auf **hinzufügen**, klicken Sie dann auf **MVC 5-Ansichtsseite mit Layout (Razor)**.
  
![](adding-a-view/_static/image1.png)   
  
In der **Namen für Artikel angeben** Dialogfeld Geben Sie *Index*, und klicken Sie dann auf **OK**.  
  
![](adding-a-view/_static/image2.png)  
  
In der **Layoutseite auswählen** Dialogfeld den Standardpfad  **\_Layout.cshtml** , und klicken Sie auf **OK**.  
  
![](adding-a-view/_static/image3.png)  
  
Im Dialogfeld für die oben genannten der *Views\Shared* Ordner im linken Bereich ausgewählt ist. Wenn Sie eine benutzerdefiniertes Layout-Datei in einen anderen Ordner haben, können Sie es auswählen. Wir sprechen über die Layoutdatei später in diesem tutorial

Die *MvcMovie\Views\HelloWorld\Index.cshtml* Datei erstellt wird.

![](adding-a-view/_static/image4.png)

Fügen Sie das folgende hervorgehobene Markup hinzu.

[!code-cshtml[Main](adding-a-view/samples/sample2.cshtml?highlight=4-11)]

Klicken Sie mit der rechten Maustaste auf die *"Index.cshtml"* und wählen Sie **in Browser anzeigen**.

![PI](adding-a-view/_static/image5.png)

Sie können auch nach rechts klicken, die *"Index.cshtml"* und wählen Sie **in Seitenprüfung anzeigen.** Finden Sie unter den [Page Inspector Tutorial](../../views/using-page-inspector-in-aspnet-mvc.md) für Weitere Informationen.

Alternativ führen Sie die Anwendung, und navigieren Sie zu der `HelloWorld` Controller (`http://localhost:xxxx/HelloWorld`). Die `Index` -Methode in Ihrer Controller nicht viel Arbeit ausführen; es einfach ausgeführt wurde, die Anweisung `return View()`, die angab, dass die Methode eine ansichtsvorlagendatei verwenden soll, um eine Antwort an den Browser auszugeben. Da Sie den Namen der ansichtsvorlagendatei mit nicht explizit angegeben haben, ASP.NET MVC standardmäßig die *"Index.cshtml"* -Datei in die *\Views\HelloWorld* Ordner. Die folgende Abbildung zeigt die Zeichenfolge &quot;Hello from our View Template!&quot; in der Ansicht hartcodiert.

![](adding-a-view/_static/image6.png)

Das sieht recht gut. Beachten Sie jedoch, dass die Titelleiste des Browsers wird &quot;Index - meine ASP.NET Anwendungse "und der große Link oben auf der Seite"Anwendungsname". Je nach kleine Sie Ihr Browserfenster vornehmen, müssen Sie möglicherweise die drei Balken in der oberen rechten Ecke, um anzuzeigen, klicken Sie auf die auf die **Startseite**, **zu**, **wenden Sie sich an**, **Registrieren** und **melden Sie sich bei** Links.

## <a name="changing-views-and-layout-pages"></a>Ändern von Ansichten und Layoutseiten

Zunächst, den Sie ändern möchten die &quot;Anwendungsname&quot; Link am oberen Rand der Seite. Dieser Text ist üblich, jede Seite. Es ist tatsächlich in nur an einer Stelle im Projekt implementiert, obwohl er auf jeder Seite in der Anwendung angezeigt wird. Wechseln Sie zu der */Views/Shared* Ordner **Projektmappen-Explorer** , und öffnen Sie die  *\_Layout.cshtml* Datei. Diese Datei heißt eine *Layoutseite* und befindet sich in den freigegebenen Ordner, die alle anderen Seiten zu verwenden.

![_LayoutCshtml](adding-a-view/_static/image7.png)

Layoutvorlagen können Sie die HTML-Containerlayout Ihrer Website zentral anzugeben und wenden Sie es über mehrere Seiten auf Ihrer Website. Suchen Sie die Zeile `@RenderBody()`. `RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite &quot;umschlossen&quot; angezeigt werden. Angenommen, Sie wählen die **zu** Link die *Views\Home\About.cshtml* Ansicht gerendert wird, in der `RenderBody` Methode.

Ändern Sie den Inhalt des Elements „title“. Ändern der [ActionLink](https://msdn.microsoft.com/library/dd504972(v=vs.108).aspx) in der Layoutvorlage aus &quot;Anwendungsname&quot; zu &quot;MVC Movie&quot; und den Controller von `Home` zu `Movies`. Die vollständige Layoutdatei ist unten dargestellt:

[!code-cshtml[Main](adding-a-view/samples/sample3.cshtml?highlight=6,20)]

Ausführen der Anwendung und beachten Sie, der jetzt angeblich &quot;MVC Movie &quot;. Klicken Sie auf die **zu** Link, und Sie sehen wie diese Seite zeigt &quot;MVC Movie&quot;ebenfalls. Wir konnten für die Änderung einmal in der Layoutvorlage aus, und haben alle Seiten auf der Website entsprechend den neuen Titel.

![](adding-a-view/_static/image8.png)

Wenn wir zuerst erstellt die *Views\HelloWorld\Index.cshtml* -Datei, sie enthalten den folgenden Code:

[!code-cshtml[Main](adding-a-view/samples/sample4.cshtml)]

Der obige Razor-Code wird die Seite "Layout" explizit festlegen. Überprüfen Sie die *Ansichten\\_ViewStart.cshtml* Datei er das genaue derselbe Razor-Markup enthält. Die *[Ansichten\\_ViewStart.cshtml](https://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx)* -Datei definiert das allgemeine Layout, das alle Ansichten, daher können Sie kommentieren, out oder entfernen Sie diesen Code aus der *Views\HelloWorld\ Datei "Index.cshtml"* Datei.

[!code-cshtml[Main](adding-a-view/samples/sample5.cshtml?highlight=1-3)]

Sie können mithilfe der `Layout`-Eigenschaft eine andere Layoutansicht festlegen oder diese auf `null` festlegen, damit keine Layoutdatei verwendet wird.

Jetzt ändern wir den Titel der Ansicht "Index".

Open *MvcMovie\Views\HelloWorld\Index.cshtml*. Es gibt zwei Bereiche zu ändern: zuerst der Text, der angezeigt wird in der Titelleiste des Browsers, und klicken Sie dann in die sekundäre Kopfzeile (die `<h2>` Element). Sie nehmen diese geringfügig anders vor, damit Sie erkennen können, welcher Teil der App mithilfe einer Codebearbeitung geändert wird.

[!code-cshtml[Main](adding-a-view/samples/sample6.cshtml?highlight=2,5)]

An die HTML-Titel angezeigt werden, den Code oben legt ein `Title` Eigenschaft der `ViewBag` Objekt (dieser befindet sich in der *"Index.cshtml"* Vorlage anzeigen). Beachten Sie, dass die Layoutvorlage aus ( *Views\Shared\\"_Layout.cshtml"* ) verwendet diesen Wert in der `<title>` -Element als Teil der `<head>` Teil der HTML-Code, die wir zuvor geändert.

[!code-cshtml[Main](adding-a-view/samples/sample7.cshtml?highlight=6)]

Mit diesem `ViewBag` Ansatz können Sie problemlos andere Parameter übergeben zwischen Ihrer Vorlage anzeigen und die Layoutdatei.

Führen Sie die Anwendung aus. Sie sehen, dass sich der Browsertitel, die primäre Überschrift und die sekundären Überschriften geändert haben. (Wenn die Änderungen nicht im Browser angezeigt werden, zeigen Sie ggf. zwischengespeicherten Inhalt an. Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.) Der Titel des Browsers wird erstellt, mit der `ViewBag.Title` legen wir den *"Index.cshtml"* Anzeigen der Vorlage und die zusätzlichen &quot;-Movie App&quot; in der Layoutdatei hinzugefügt.

Beachten Sie außerdem wie der Inhalt in die *"Index.cshtml"* ansichtsvorlage zusammengeführt wurde, mit der  *\_Layout.cshtml* Vorlage anzeigen und eine einzelne HTML-Antwort an den Browser gesendet wurde. Layoutvorlagen erleichtern ungemein das Vornehmen von Änderungen an allen Seiten in Ihrer Anwendung.

![](adding-a-view/_static/image9.png)

Unsere wenig &quot;Daten&quot; (in diesem Fall die &quot;Hello from our View Template!&quot; Nachricht) ist hartcodiert, jedoch. Die MVC-Anwendung verfügt über eine &quot;V&quot; (Ansicht), verfügen Sie über eine &quot;C&quot; (Controller), aber kein &quot;M&quot; (model) noch. Behandeln wir kurz, wie Sie eine Datenbank erstellen und Modellieren von Daten daraus abrufen.

## <a name="passing-data-from-the-controller-to-the-view"></a>Übergeben von Daten vom Controller an die Ansicht

Bevor wir mit einer Datenbank und über Modelle sprechen, jedoch zunächst sprechen wir über Informationen vom Controller an eine Ansicht übergeben. Controller-Klassen werden als Reaktion auf eine eingehende URL-Anforderung aufgerufen. Eine Controllerklasse ist, Schreiben Sie der Code, der den eingehende Browser verarbeitet, angefordert wird, ruft Daten aus einer Datenbank ab, und letztlich entscheidet, welche Art der Antwort zurück an den Browser senden. Anzeigen von Vorlagen können dann verwendet werden von einem Controller zum Generieren und Formatieren einer HTML-Antwortinhalts an den Browser.

Controller sind verantwortlich für das Bereitstellen der beliebige Daten oder Objekte erforderlich sind, in der Reihenfolge für eine ansichtsvorlage eine Antwort an den Browser auszugeben. Eine bewährte Methode: **Eine ansichtsvorlage sollte nie Geschäftslogik ausführen bzw. interagieren mit einer Datenbank direkt**. Stattdessen sollte eine ansichtsvorlage nur mit den Daten arbeiten, die sie durch den Controller bereitgestellt wird. Verwalten diese &quot;Trennung der Belange&quot; bleibt der Code übersichtlich, testfähig und besser verwaltbar.

Derzeit den `Welcome` Aktionsmethode in der `HelloWorldController` -Klasse eine `name` und `numTimes` Parameter und gibt anschließend die Werte direkt an den Browser. Anstatt den Controller diese Antwort als Zeichenfolge Rendern zu lassen, ändern wir den Controller, um stattdessen eine ansichtsvorlage zu verwenden. Die Ansichtsvorlage generiert eine dynamische Antwort. Das bedeutet, dass Sie die entsprechenden Datenelemente vom Controller an die Ansicht übergeben müssen, um die Antwort zu generieren. Hierzu können Sie den Controller die dynamischen Daten (Parameter) zu platzieren, die die ansichtsvorlage, in benötigt einem `ViewBag` -Objekt, das die ansichtsvorlage zugreifen kann.

Wechseln Sie zurück zur der *HelloWorldController.cs* und zu ändern der `Welcome` -Methode zum Hinzufügen einer `Message` und `NumTimes` Wert der `ViewBag` Objekt. `ViewBag` ist ein dynamisches Objekt, was bedeutet, dass die gewünschten, Sie platzieren können. die `ViewBag` Objekt verfügt über keine definierten Eigenschaften aus, bis Sie etwas darin einfügen. Die [ASP.NET MVC-modellbindungssystem](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) ordnet automatisch die benannten Parameter (`name` und `numTimes`) aus der Abfragezeichenfolge in der Adressleiste den Parametern der Methode. Die vollständige Datei *HelloWorldController.cs* sieht wie folgt aus:

[!code-csharp[Main](adding-a-view/samples/sample8.cs)]

Jetzt die `ViewBag` Objekt enthält Daten, die automatisch an die Ansicht übergeben werden. Als Nächstes fügen Sie eine ansichtsvorlage! In der **erstellen** , wählen Sie im Menü **Projektmappe** (oder STRG + UMSCHALT + B) um sicherzustellen, dass das Projekt kompiliert wird. Klicken Sie mit der rechten Maustaste auf die *Views\HelloWorld* Ordner, und klicken Sie auf **hinzufügen**, klicken Sie dann auf **MVC 5-Ansichtsseite mit Layout (Razor)**.
  
![](adding-a-view/_static/image10.png)   
  
In der **Namen für Artikel angeben** Dialogfeld Geben Sie *Willkommen*, und klicken Sie dann auf **OK**.   
  
In der **Layoutseite auswählen** Dialogfeld den Standardpfad  **\_Layout.cshtml** , und klicken Sie auf **OK**.  
  
![](adding-a-view/_static/image11.png)   

Die *MvcMovie\Views\HelloWorld\Welcome.cshtml* Datei erstellt wird.

Ersetzen Sie das Markup in der *Welcome.cshtml* Datei. Erstellen Sie eine Schleife, die besagt, &quot;Hello&quot; so oft wie der Benutzer sagt sollte. Die vollständige *Welcome.cshtml* Datei ist unten dargestellt.

[!code-cshtml[Main](adding-a-view/samples/sample9.cshtml)]

Führen Sie die Anwendung, und navigieren Sie zur folgenden URL:

`http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4`

Jetzt Daten der URL entnommen und übergeben dem Controller mithilfe der [model Binders](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx). Der Controller Packt die Daten in einem `ViewBag` Objekt und übergibt das Objekt an die Ansicht. Die Ansicht zeigt dann die Daten im HTML-Format für den Benutzer.

![](adding-a-view/_static/image12.png)

Im obigen Beispiel haben wir verwendet eine `ViewBag` Objekt, das Daten vom Controller an eine Ansicht zu übergeben. Später in diesem Tutorial verwenden wir eine Ansichtsmodell, um Daten von einem Controller an eine Ansicht zu übergeben. Die ViewModel-Ansatz zum Übergeben von Daten wird über den Ansicht-Behälter-Ansatz im Allgemeinen vorzuziehen. Finden Sie im Blogeintrag [dynamische V stark typisierte Ansichten](https://blogs.msdn.com/b/rickandy/archive/2011/01/28/dynamic-v-strongly-typed-views.aspx) für Weitere Informationen. 

Nun, das eine Art wurde von einem &quot;M&quot; für Modell, jedoch nicht der Art Datenbank. Lassen Sie uns das Gelernte umsetzen und eine Filmdatenbank erstellen.

> [!div class="step-by-step"]
> [Zurück](adding-a-controller.md)
> [Weiter](adding-a-model.md)
