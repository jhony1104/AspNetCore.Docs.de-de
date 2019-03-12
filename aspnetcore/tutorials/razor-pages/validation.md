---
title: Hinzufügen der Validierung zu einer Razor-Seite in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie einer Razor-Seite in ASP.NET Core Validierung hinzugefügt wird.
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 961b41a63a5dd066a51468239bd855b7f723a3ae
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346040"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Hinzufügen der Validierung zu einer Razor-Seite in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt wird dem Modell `Movie` Validierungslogik hinzugefügt. Die Validierungsregeln werden erzwungen, wenn ein Benutzer einen Film erstellt oder bearbeitet.

## <a name="validation"></a>Validierung

Ein wesentlicher Grundsatz der Softwareentwicklung heißt [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (**D**on't **R**epeat **Y**ourself, dt. Wiederholen Sie sich nicht). Razor Pages sind für Entwicklungsaufgaben gedacht, bei denen Funktionalität einmal angegeben und in der gesamten App übernommen wird. DRY kann Sie bei Folgendem unterstützen:

* Sie können die Codemenge in einer App reduzieren.
* Der Code wird weniger fehleranfällig und lässt sich leichter testen und verwalten.

Die von Razor Pages und Entity Framework gebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips. Validierungsregeln werden an zentraler Stelle (in der Modellklasse) deklarativ angegeben und überall in der App erzwungen.

### <a name="adding-validation-rules-to-the-movie-model"></a>Hinzufügen von Validierungsregeln zum Modell „Movie“

Öffnen Sie die Datei *Models/Movie.cs*. [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) bietet eine integrierte Gruppe von Validierungsattributen, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden. DataAnnotations enthält auch Formatierungsattribute wie `DataType`, die bei der Formatierung helfen und keine Validierung bieten.

Aktualisieren Sie die `Movie`-Klasse, um die Validierungsattribute `Required`, `StringLength`, `RegularExpression` und `Range` zu nutzen.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Validierungsattribute geben das Verhalten an, das für Modelleigenschaften erzwungen wird:

* Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft über einen Wert verfügen muss. Nichts hindert Benutzer allerdings daran, Leerzeichen einzugeben, um die Validierungseinschränkung für einen Nullable-Typ zu umgehen. [Werttypen](/dotnet/csharp/language-reference/keywords/value-types), bei denen es sich nicht um Nullable-Typen handelt (wie z.B. `decimal`, `int`, `float` und `DateTime`), sind grundsätzlich erforderlich und benötigen das Attribut `Required` nicht.
* Das Attribut `RegularExpression` schränkt die Zeichen ein, die ein Benutzer eingeben kann. Im vorangehenden Code muss `Genre` mit mindestens einem Großbuchstaben beginnen, dem null oder mehr Buchstaben, einfache oder doppelte Anführungszeichen, Leerzeichen oder Bindestriche folgen. `Rating` muss mit mindestens einem Großbuchstaben beginnen, dem null oder mehr Buchstaben, Zahlen, einfache oder doppelte Anführungszeichen, Leerzeichen oder Bindestriche folgen.
* Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.
* Das Attribut `StringLength` legt die Höchstlänge einer Zeichenfolge und optional die Mindestlänge fest. 

Dadurch, dass Validierungsregeln von ASP.NET Core automatisch erzwungen werden, wird eine App stabiler. Die automatische Validierung für Modelle hilft beim Schutz der App, da Sie nicht daran denken müssen, diesen anzuwenden, sobald neuer Code hinzugefügt wird.

### <a name="validation-error-ui-in-razor-pages"></a>Benutzeroberflächenoption für Validierungsfehler in Razor Pages

Führen Sie die App aus, und navigieren Sie zu „Pages/Movies“.

Klicken Sie auf den Link **Neu erstellen**. Füllen Sie das Formular mit einigen ungültigen Werten aus. Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Wie Sie sehen, hat das Formular in allen Feldern mit einem ungültigen Wert automatisch eine Validierungsfehlermeldung angezeigt. Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).

Ein entscheidender Vorteil ist, dass **keine** Codeänderungen auf den Seiten „Erstellen“ oder „Bearbeiten“ erforderlich waren. Nach Anwenden von „DataAnnotations“ auf das Modell wurde die Benutzeroberflächenvalidierung aktiviert. Die in diesem Tutorial erstellten Razor Pages haben die Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`). Testen Sie die Validierung mithilfe der Seite „Bearbeiten“. Es erfolgt dieselbe Validierung.

Die Formulardaten werden erst an den Server zurückgesendet, wenn auf Clientseite keine Validierungsfehler auftreten. Überprüfen Sie mithilfe von mindestens einem der folgenden Ansätze, ob keine Formulardaten bereitgestellt werden:

* Setzen Sie einen Haltepunkt in der `OnPostAsync`-Methode. Senden Sie das Formular (wählen Sie **Erstellen** oder **Speichern**). Der Haltepunkt wird niemals erreicht.
* Verwenden Sie das [Tool Fiddler](http://www.telerik.com/fiddler).
* Verwenden Sie die Browserentwicklungstools zum Überwachen des Netzwerkdatenverkehrs.

### <a name="server-side-validation"></a>Serverseitige Validierung

Wenn JavaScript im Browser deaktiviert ist, erfolgt beim Senden des Formulars mit Fehlern eine Bereitstellung auf dem Server.

Testen Sie optional die serverseitige Validierung:

* Deaktivieren Sie JavaScript im Browser. Sie erreichen dies mithilfe der Entwicklertools Ihres Browsers. Wenn Sie JavaScript im Browser nicht deaktivieren können, probieren Sie einen anderen Browser.
* Setzen Sie auf der Seite „Erstellen“ oder „Bearbeiten“ in der `OnPostAsync`-Methode einen Haltepunkt.
* Senden Sie ein Formular mit Validierungsfehlern.
* Überprüfen Sie, ob der Modellstatus ungültig ist:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

Der folgende Code zeigt einen Teil der Seite *Create.cshtml*, deren Gerüst Sie zuvor im Tutorial erstellt haben. Sie wird von den Seiten „Erstellen“ und „Bearbeiten“ zum Anzeigen des anfänglichen Formulars und zum erneuten Anzeigen des Formulars bei einem Fehler verwendet.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler. Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).

Die Seiten „Erstellen“ und „Bearbeiten“ weisen keine Validierungsregeln auf. Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben. Diese Validierungsregeln gelten automatisch für Razor Pages, die das Modell `Movie` bearbeiten.

Wenn Validierungslogik geändert werden muss, erfolgt dies nur im Modell. Die Validierung erfolgt in der gesamten Anwendung einheitlich (Validierungslogik ist zentral definiert). Die zentrale Validierung unterstützt sauberen Code und erleichtert dessen Verwaltung und Aktualisierung.

## <a name="using-datatype-attributes"></a>Verwenden von „DataType“-Attributen

Untersuchen Sie die Klasse `Movie`. Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit. Das `DataType`-Attribut wird nur auf die Eigenschaften `ReleaseDate` und `Price` angewendet.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Die `DataType`-Attribute geben der Anzeige-Engine nur Hinweise zum Formatieren der Daten (und liefern Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail). Verwenden Sie das `RegularExpression`-Attribut, um das Format der Daten zu validieren. Das `DataType`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist. `DataType`-Attribute sind keine Validierungsattribute. In der Beispielanwendung wird nur das Datum ohne Uhrzeit angezeigt.

Die `DataType`-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“ usw. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Ein Link des Typs `mailto:` kann beispielsweise für `DataType.EmailAddress` erstellt werden. In Browsern mit HTML5-Unterstützung kann für `DataType.Date` eine Datumsauswahl bereitgestellt werden. Die `DataType`-Attribute geben `data-`-Attribute (ausgesprochen „Datadash“) von HTML5 aus, die HTML5-Browser nutzen. Die `DataType`-Attribute bieten **keine** Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.


Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann. Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung angewendet werden soll, wenn der Wert zur Bearbeitung angezeigt wird. Dieses Verhalten ist für einige Felder ggf. nicht wünschenswert. Beispielsweise sollte bei Währungswerten das Währungssymbol auf der Benutzeroberfläche für die Bearbeitung nicht angezeigt werden.

Das `DisplayFormat`-Attribut kann eigenständig verwendet werden, doch meist empfiehlt es sich, das `DataType`-Attribut zu verwenden. Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Es bietet die folgenden Vorteile, die „DisplayFormat“ nicht bietet:

* Der Browser kann HTML5-Features aktivieren (z.B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.).
* Standardmäßig rendert der Browser Daten mit dem ordnungsgemäßen auf Ihrem Gebietsschema basierenden Format.
* Das `DataType`-Attribut kann dem ASP.NET Core-Framework ermöglichen, die richtige Feldvorlage zum Rendern der Daten auszuwählen. `DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage.

Hinweis: Die jQuery-Validierung funktioniert nicht mit den Attributen `Range` und `DateTime`. Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Es wird allgemein nicht empfohlen, feste Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `Range`-Attributs und von `DateTime` abgeraten wird.

Der folgende Code zeigt die Kombination von Attributen in einer Zeile:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) zeigt erweiterte EF Core-Vorgänge mit Razor Pages.

### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Informationen zum Bereitstellen in Azure finden Sie unter [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase). Diese Anweisungen beziehen sich auf eine ASP.NET-App, nicht auf eine ASP.NET Core-App, aber die Schritte sind identisch.

Vielen Dank für Ihr Interesse an dieser Einführung in Razor Pages. [Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Dieses Tutorial auf YouTube](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Zurück: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)
