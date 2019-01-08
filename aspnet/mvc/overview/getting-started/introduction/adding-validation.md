---
uid: mvc/overview/getting-started/introduction/adding-validation
title: Hinzufügen einer Validierung | Microsoft-Dokumentation
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 9f35ca15-e216-4db6-9ebf-24380b0f31b4
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-validation
msc.type: authoredcontent
ms.openlocfilehash: 6831259ce19c3747c179d6fc1b7e2095051a603b
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099025"
---
<a name="adding-validation"></a>Hinzufügen der Validierung
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

In diesem Abschnitt fügen Sie Validierungslogik auf die `Movie` Modell, und stellen sicher, dass die Validierungsregeln immer, die ein Benutzer versucht erzwungen werden, erstellen oder Bearbeiten eines Films mit der Anwendung.

## <a name="keeping-things-dry"></a>Halten Dinge DRY

Einer der Entwurfsgrundsätze von ASP.NET MVC Core ist [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself) (&quot;wiederhole Dich nicht&quot;). ASP.NET MVC empfiehlt Ihnen, Funktionalität und Verhalten nur einmal angeben, und klicken Sie dann es überall in einer Anwendung berücksichtigt werden. Dadurch reduziert die Menge an Code, den Sie schreiben müssen und den Code, den Sie weniger Fehler verursachenden und einfacher schreiben zu verwalten.

ASP.NET MVC und Entity Framework Code First angebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips in Aktion. Sie können deklarativ Validierungsregeln an zentraler Stelle (in der Modellklasse) angeben und die Regeln werden überall in der Anwendung erzwungen.

Sehen wir uns, wie Sie diese überprüfungsunterstützung in der Movie-Anwendung nutzen können.

## <a name="adding-validation-rules-to-the-movie-model"></a>Hinzufügen von Validierungsregeln zum Modell "Movie"

Sie beginnen mit der Überprüfung Programmlogik zum Hinzufügen der `Movie` Klasse.

Öffnen Sie Datei *Movie.cs*. Beachten Sie, dass die [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) Namespace enthält keine `System.Web`. "DataAnnotations" bietet einen integrierten Satz von Validierungsattributen, die Sie deklarativ auf eine Klasse oder Eigenschaft anwenden können. (Es enthält auch Formatierungsattribute wie [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) , bei der Formatierung helfen und keine Validierung bieten.)

Aktualisieren Sie jetzt die `Movie` Klasse nutzen der integrierten [ `Required` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx), [ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx), [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx), und [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) Validierungsattribute. Ersetzen Sie die `Movie` Klasse durch Folgendes:

[!code-csharp[Main](adding-validation/samples/sample1.cs?highlight=5,13-15,18-19,22-23)]

Die [ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) -Attribut legt die maximale Länge der Zeichenfolge ist, wird diese Einschränkung für die Datenbank und aus diesem Grund wird das Datenbankschema geändert. Klicken Sie mit der rechten Maustaste auf die **Filme** -Tabelle **Server-Explorer** , und klicken Sie auf **Tabellendefinition öffnen**:

![](adding-validation/_static/image1.png)

In der obigen Abbildung sehen Sie, alle die Zeichenfolgenfelder sind auf [NVARCHAR (MAX)](https://technet.microsoft.com/library/ms186939.aspx). Wir verwenden Migrationen zum Aktualisieren des Schemas. Erstellen Sie die Projektmappe, und öffnen Sie die **-Paket-Manager-Konsole** Fenster, und geben Sie die folgenden Befehle aus:

[!code-console[Main](adding-validation/samples/sample2.cmd)]

Wenn dieser Befehl abgeschlossen ist, handelt es sich bei Visual Studio öffnet die Klassendatei, die die neue definiert `DbMIgration` abgeleitete Klasse mit dem angegebenen Namen (`DataAnnotations`), und klicken Sie in der `Up` Methode sehen Sie den Code, der die schemaeinschränkungen aktualisiert:

[!code-csharp[Main](adding-validation/samples/sample3.cs)]

Die `Genre` Feld ist nicht mehr NULL-Werte zulässt (d. h. Sie müssen ein einen Wert eingeben). Die `Rating` Feld hat eine maximale Länge von 5 und `Title` hat eine maximale Länge von 60. Die minimale Länge von 3 auf `Title` und des Bereichs auf `Price` schemaänderungen nicht erstellt wurde.

Überprüfen Sie das Schema des Films:

![](adding-validation/_static/image2.png)

Die Zeichenfolgenfelder angezeigt, die neuen Grenzwerte für die Länge und `Genre` nicht mehr als auf NULL festlegbar aktiviert ist.

Die Validierungsattribute geben das Verhalten an, das Sie in den Modelleigenschaften erzwingen möchten, auf die sie angewendet werden. Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Validierung zu erfüllen. Die [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) Attribut wird verwendet, um einzuschränken, welche Zeichen sein können Eingabe. Im oben angegebenen Code sind für `Genre` und `Rating` nur Buchstaben (keine Leerzeichen, Zahlen und Sonderzeichen) erlaubt. Die [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) Attribut schränkt einen Wert innerhalb eines angegebenen Bereichs. Mit dem Attribut `StringLength` können Sie die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegen. Werttypen (z. B. `decimal, int, float, DateTime`) sind grundsätzlich erforderlich und müssen nicht die `Required` Attribut.

Code wird zunächst an, dass die Validierungsregeln, die Sie, auf eine Modellklasse angeben erzwungen werden, bevor die Anwendung Änderungen in der Datenbank speichert. Beispielsweise löst der folgende Code eine [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception(v=vs.103).aspx) Ausnahme bei der `SaveChanges` -Methode aufgerufen wird, da einige erforderliche `Movie` Eigenschaftswerte sind nicht vorhanden:

[!code-csharp[Main](adding-validation/samples/sample4.cs)]

Der obige Code wird die folgende Ausnahme ausgelöst:

*Fehler bei der Überprüfung für eine oder mehrere Entitäten. Finden Sie unter 'EntityValidationErrors'-Eigenschaft für die weitere Details.*

Dadurch, dass Validierungsregeln automatisch erzwungen, die von .NET Framework machen hilft Ihrer Anwendung stabiler. Darüber hinaus wird sichergestellt, dass Sie die Validierung nicht vergessen und nicht versehentlich falsche Daten in die Datenbank übernehmen.

## <a name="validation-error-ui-in-aspnet-mvc"></a>Fehler bei der Validierung Benutzeroberfläche in ASP.NET MVC

Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL.

Klicken Sie auf die **neu erstellen** Link, um einen neuen Film hinzuzufügen. Füllen Sie das Formular mit einigen ungültigen Werten aus. Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.

![8_validationErrors](adding-validation/_static/image3.png)

> [!NOTE]
> zur Unterstützung von jQuery-Validierung für nicht englische Gebietsschemas, in denen ein Komma (",") für ein Dezimaltrennzeichen, müssen Sie die NuGet einschließen Globalisieren wie zuvor in diesem Tutorial beschrieben.


Beachten Sie, wie das Formular automatisch eine roten Rahmenfarbe verwendet wurde um den Inhalt der Textfelder hervorzuheben, die ungültige Daten enthalten, und verfügt über eine entsprechende Validierungsfehlermeldung neben jeder ausgegeben. Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).

Ein echter Vorteil ist, dass Sie nicht in eine einzige Codezeile ändern müssen die `MoviesController` Klasse oder in der *Create.cshtml* anzeigen, um diese Benutzeroberfläche für die Validierung zu aktivieren. Die Controller und Ansichten, die Sie zuvor in diesem Tutorial erstellt haben, haben die angegebenen Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`). Testen Sie die Validierung mithilfe der Aktionsmethode `Edit`, und es folgt die gleiche Validierung.

Die Formulardaten werden erst an den Server gesendet, wenn auf Clientseite keine Validierungsfehler mehr auftreten. Sie können dies überprüfen, indem Sie Sie in der HTTP-Post-Methode, einen Haltepunkt einfügen, mit der [Fiddler-Tool](http://fiddler2.com/fiddler2/), oder der Internet Explorer [F12-Entwicklertools](https://msdn.microsoft.com/ie/aa740478).

## <a name="how-validation-occurs-in-the-create-view-and-create-action-method"></a>Wie in der Validierung erstellen, anzeigen, und Erstellen von Action-Methode

Sie fragen sich vielleicht, wie die Benutzeroberfläche für die Validierung ohne Aktualisierungen von Code im Controller oder in Ansichten generiert wurde. Die nächste Liste wird gezeigt, wie die `Create` Methoden in der `MovieController` Klasse aus. Es handelt sich gegenüber, wie Sie diese zuvor in diesem Tutorial erstellt haben.

[!code-csharp[Main](adding-validation/samples/sample5.cs)]

Die erste `Create`-Aktionsmethode (HTTP GET) zeigt das erste Formular „Create“ an. Die zweite Version (`[HttpPost]`) verarbeitet die Formularbereitstellung. Die zweite `Create` Methode (die `HttpPost` Version) überprüft `ModelState.IsValid` um festzustellen, ob der Film Validierungsfehler aufweist. Beim Abrufen dieser Eigenschaft wertet alle Validierungsattribute, die auf das Objekt angewendet wurden. Wenn das Objekt Validierungsfehler enthält die `Create` -Methode zeigt das Formular. Wenn keine Fehler vorliegen, speichert die Methode den neuen Film in der Datenbank. In unserem filmbeispiel wird **Form wird nicht an den Server gesendet, wenn Validierungsfehler erkannt werden, auf der Clientseite; es gibt die zweite** `Create` **wird nie aufgerufen**. Wenn Sie JavaScript in Ihrem Browser deaktivieren, wird die Clientvalidierung deaktiviert und die HTTP-POST `Create` Methode ruft `ModelState.IsValid` zu überprüfen, ob der Film Validierungsfehler aufweist.

Sie können einen Haltepunkt in der `HttpPost Create`-Methode festlegen und überprüfen, ob die Methode tatsächlich niemals aufgerufen wird und die clientseitige Validierung die Formulardaten nicht sendet, wenn Validierungsfehler gefunden werden. Wenn Sie JavaScript in Ihrem Browser deaktivieren und dann das Formular mit Fehlern senden, wird der Haltepunkt erreicht. Sie erhalten auch ohne JavaScript weiterhin eine vollständige Validierung. Die folgende Abbildung zeigt, wie JavaScript in Internet Explorer deaktiviert.

![](adding-validation/_static/image5.png)

![](adding-validation/_static/image6.png)

Die folgende Abbildung zeigt, wie JavaScript im Firefox-Browser deaktiviert wird.

![](adding-validation/_static/image7.png)

Die folgende Abbildung zeigt, wie JavaScript im Chrome-Browser deaktiviert wird.

![](adding-validation/_static/image8.png)

Im folgenden finden Sie die *Create.cshtml* Vorlage anzeigen, die Sie zuvor in diesem Tutorial erstellt haben. Sie wird von den oben erläuterten Aktionsmethoden zum Anzeigen des anfänglichen Formulars und zum erneuten Anzeigen des Formulars bei einem Fehler verwendet.

[!code-cshtml[Main](adding-validation/samples/sample6.cshtml?highlight=16-17)]

Beachten Sie, wie der Code verwendet ein `Html.EditorFor` Hilfsmethode zum Ausgeben der `<input>` -Element für jede `Movie` Eigenschaft. Neben dieses Hilfsprogramm ist ein Aufruf der `Html.ValidationMessageFor` Hilfsmethode. Diese zwei Hilfsmethoden arbeiten mit dem Modellobjekt, das vom Controller an die Ansicht übergeben wird (in diesem Fall eine `Movie` Objekt). Diese Suche automatisch für Validierungsattribute, die für die Fehlermeldungen Modell und die Anzeige entsprechend angegeben.

Wirklich nützlich an diesem Ansatz ist, dass weder der Controller noch die Ansichtsvorlage `Create` an den eigentlichen Validierungsregeln, die erzwungen werden, oder den spezifischen Fehlermeldungen, die angezeigt werden, beteiligt sind. Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben. Diese gleichen Validierungsregeln werden automatisch auf die Ansicht `Edit` und alle anderen Ansichtsvorlagen angewendet, die Sie erstellen und die das Modell bearbeiten.

Wenn Sie die Validierungslogik später ändern möchten, erreichen Sie dies an genau einer Stelle durch Hinzufügen der Validierungsattribute zum Modell (in diesem Beispiel die `movie` Klasse). Sie müssen sich keine Gedanken darüber machen, ob die verschiedenen Teile der Anwendung inkonsistent sind und wie Regeln erzwungen werden: Die gesamte Validierungslogik wird zentral definiert und überall verwendet. Dies hält den Code sehr übersichtlich und vereinfacht die Verwaltung und Entwicklung. Und dies bedeutet, dass Sie vollständig berücksichtigt werden, werden die *DRY* Prinzip.

## <a name="using-datatype-attributes"></a>Verwenden von „DataType“-Attributen

Öffnen Sie die Datei *Movie.cs*, und überprüfen Sie die Klasse `Movie`. Die [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) -Namespace stellt neben dem integrierten Satz von Validierungsattributen Formatierungsattribute bereit. Wir haben bereits angewendet. eine [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) Enumerationswert, um das Veröffentlichungsdatum und Preisfelder. Der folgende code zeigt die `ReleaseDate` und `Price` Eigenschaften mit dem entsprechenden [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) Attribut.

[!code-csharp[Main](adding-validation/samples/sample7.cs)]

Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribute geben nur die Hinweise für die Anzeige-Engine zum Formatieren der Daten (und liefern Attribute wie z. B. `<a>` für URLs und `<a href="mailto:EmailAddress.com">` -e-Mail. Sie können die [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) Attribut, um das Format der Daten zu überprüfen. Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist, sind sie ***nicht*** Validierungsattribute. In diesem Fall soll nur das Datum verfolgt werden, nicht das Datum und die Zeit. Die [DataType-Enumeration](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) stellt viele Datentypen, z. B. *Datum "," Uhrzeit "," PhoneNumber "," Währung "," EmailAddress* und vieles mehr. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Z. B. eine `mailto:` Link erstellt werden kann, für die [DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx), und eine Datumsauswahl kann angegeben werden, für die [DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) in Browsern mit Unterstützung [HTML5](http://html5.org/). Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribute gibt HTML 5 [Data -](http://ejohn.org/blog/html-5-data-attributes/) (ausgesprochen als *Daten Dash*) Attribute, die HTML5-Browsern genutzt werden können. Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribute bieten keine Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf dem Server des [CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:


[!code-csharp[Main](adding-validation/samples/sample8.cs)]


Die `ApplyFormatInEditMode` Einstellung gibt an, dass die angegebene Formatierung auch angewendet werden soll, wenn der Wert in einem Textfeld zur Bearbeitung angezeigt wird. (Möchten Sie vielleicht nicht, die für einige Felder, z. B. für Währungsangaben nicht empfiehlt das Währungssymbol im Textfeld für die Bearbeitung.)

Können Sie die [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) Attribut selbst, aber es ist im Allgemeinen eine gute Idee, verwenden Sie die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) auch Attribut. Die `DataType` -Attribut übermittelt die *Semantik* der Daten als dagegen spricht, wie sie auf dem Bildschirm gerendert, und bietet die folgenden Vorteile, die nicht im Lieferumfang enthalten `DisplayFormat`:

- Der Browser kann HTML5-Features (z. B. zum Anzeigen eines Kalendersteuerelements, dem Gebietsschema entsprechenden Währungssymbols, e-Mail-Links usw.) aktivieren.
- Standardmäßig rendert der Browser Daten mit dem richtigen Format basierend auf Ihrer [Gebietsschema](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx).
- Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) Attribut kann MVC die richtige Feldvorlage zum Rendern der Daten auswählen zu aktivieren (die [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) von verwendet selbst verwendet die zeichenfolgenvorlage). Weitere Informationen finden Sie in Brad Wilsons [ASP.NET MVC 2-Vorlagen](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (Obwohl für MVC 2 geschrieben wird, gilt in diesem Artikel noch auf die aktuelle Version von ASP.NET MVC.)

Bei Verwendung der `DataType` Attribut mit einem Datumsfeld müssen Sie angeben der `DisplayFormat` Attribut auch, um sicherzustellen, dass das Feld in Chrome-Browsern richtig gerendert wird. Weitere Informationen finden Sie unter [dieser Stack Overflow-Thread](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

> [!NOTE]
> jQuery-Validierung funktioniert nicht mit der [Bereich](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) Attribut und ["DateTime"](https://msdn.microsoft.com/library/system.datetime.aspx). Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:
> 
> [!code-csharp[Main](adding-validation/samples/sample9.cs)]
> 
> Sie müssen mit jQuery-datumsvalidierung Deaktivieren der [Bereich](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) -Attribut mit ["DateTime"](https://msdn.microsoft.com/library/system.datetime.aspx). Es ist im Allgemeinen nicht empfohlen, feste Datumsangaben in Ihren Modellen, also Kompilieren der [Bereich](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) Attribut und ["DateTime"](https://msdn.microsoft.com/library/system.datetime.aspx) wird davon abgeraten.


Der folgende Code zeigt die Kombination von Attributen in einer Zeile:

[!code-csharp[Main](adding-validation/samples/sample10.cs?highlight=4,6,10,12)]

Im nächsten Teil der Reihe überprüfen wir die Anwendung und nehmen einige Verbesserungen an den automatisch generierten Methoden `Details` und `Delete` vor.

> [!div class="step-by-step"]
> [Zurück](adding-a-new-field.md)
> [Weiter](examining-the-details-and-delete-methods.md)
