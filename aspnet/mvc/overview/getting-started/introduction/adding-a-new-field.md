---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Hinzufügen eines neuen Felds | Microsoft-Dokumentation
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 950ae17ebd6b0f15520c2a4e9372703f5374dfbe
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098687"
---
<a name="adding-a-new-field"></a>Hinzufügen eines neuen Felds
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

In diesem Abschnitt verwenden Sie Entity Framework Code First-Migrationen, migrieren einige Änderungen in der ViewModel-Klassen, damit die Änderung auf die Datenbank angewendet wird.

In der Standardeinstellung, wenn Sie Entity Framework Code First verwenden, um automatisch eine Datenbank erstellen, wie weiter oben in diesem Tutorial Code First eine Tabelle der Datenbank hinzugefügt, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, die sie aus generiert wurde. Wenn sie nicht synchron sind, löst Entity Framework einen Fehler aus. Dies erleichtert es, die zum Zeitpunkt der Entwicklung Identifizieren von Problemen, die andernfalls nur (durch ungewöhnliche Fehler) zur Laufzeit auftreten.

## <a name="setting-up-code-first-migrations-for-model-changes"></a>Zum Einrichten von Code First-Migrationen Änderungen des Datenmodells

Navigieren Sie zum Projektmappen-Explorer. Klicken Sie mit der rechten Maustaste auf die *Movies.mdf* und wählen Sie **löschen** zum Entfernen der Datenbank Filme. Wenn Sie nicht sehen die *Movies.mdf* Datei, klicken Sie auf die **alle Dateien anzeigen** Symbol unten im roten Feld.

![](adding-a-new-field/_static/image1.png)

Erstellen Sie die Anwendung aus, um sicherzustellen, dass keine Fehler vorliegen.

Von der **Tools** Menü klicken Sie auf **NuGet Package Manager** und dann **-Paket-Manager-Konsole**.

![Pack Man hinzufügen](adding-a-new-field/_static/image2.png)

In der **-Paket-Manager-Konsole** Fenster beim der `PM>` Eingabeaufforderung eingeben.

Enable-Migrations "– ContextTypeName" MvcMovie.Models.MovieDBContext

![](adding-a-new-field/_static/image3.png)

Die **Enable-Migrations** (siehe oben) Befehl erstellt eine *Configuration.cs* Datei in einem neuen *Migrationen* Ordner.

![](adding-a-new-field/_static/image4.png)

Visual Studio öffnet die *Configuration.cs* Datei. Ersetzen Sie die `Seed` -Methode in der die *Configuration.cs* -Datei mit den folgenden Code:

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

Zeigen Sie auf die rote Wellenlinie unter `Movie` , und klicken Sie auf `Show Potential Fixes` , und klicken Sie dann auf **mit** **MvcMovie.Models;**

![](adding-a-new-field/_static/image5.png)

Auf diese Weise fügt die folgenden using-Anweisung:

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> Code First-Migrationen Ruft die `Seed` -Methode auf, nachdem jede Migration (d. h. Aufrufen **Update-Database** in der Paket-Manager-Konsole), und diese Methode aktualisiert die Zeilen, die bereits eingefügt wurde, oder fügt sie an, wenn sie nicht noch vorhanden ist.
> 
> Die [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) -Methode in der folgende Code führt einen "Upsert"-Vorgang:
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> Da die [Ausgangswert](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) Methode, die bei jeder Migration ausgeführt wird, Daten, können nicht nur eingefügt werden, da die Zeilen, die Sie hinzufügen möchten es nach der ersten Migration sein werden, der die Datenbank erstellt. Die "[" Upsert "](http://en.wikipedia.org/wiki/Upsert)" Vorgang wird verhindert, dass Fehler, die passieren würde, wenn Sie versuchen, eine Zeile einzufügen, die bereits vorhanden ist, aber überschreibt es alle Änderungen an Daten, die Sie möglicherweise beim Testen der Anwendung vorgenommen haben. Mit Testdaten in einigen Tabellen möchten Sie vielleicht nicht so: in einigen Fällen, wenn Sie Daten während des Tests ändern, sollen die Änderungen, um nach Datenbankupdates bleiben. In diesem Fall einen bedingte Insert-Vorgang ausgeführt werden soll: Fügen Sie eine Zeile nur dann, wenn er nicht bereits vorhanden.   
> 
> Der erste Parameter zu übergeben, um die [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) Methode gibt die Eigenschaft zu verwenden, um zu überprüfen, ob eine Zeile bereits vorhanden ist. Für die Testdaten für den Film, die Sie bereitstellen, die `Title` Eigenschaft kann für diesen Zweck verwendet werden, da jeder Titel in der Liste eindeutig ist:
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> Dieser Code wird davon ausgegangen, dass Titel eindeutig sind. Wenn Sie manuell einen doppelten Titel hinzufügen, erhalten Sie die folgende Ausnahme beim nächsten einer Migration ausführen.   
> 
>  *Die Sequenz enthält mehr als ein element*  
> 
> Weitere Informationen zu den [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) -Methode finden Sie unter [kümmern mit EF 4.3 AddOrUpdate Methode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...


**Drücken Sie STRG + UMSCHALT + B, um das Projekt erstellen.** (Die folgenden Schritte aus schlägt fehl, wenn Sie an diesem Punkt nicht erstellt werden.)

Der nächste Schritt ist die Erstellung einer `DbMigration` -Klasse für die anfängliche Migration. Diese Migration erstellt eine neue Datenbank, die daher wird Sie gelöscht der *movie.mdf* -Datei in einem vorherigen Schritt.

In der **-Paket-Manager-Konsole** Fenster, geben Sie den Befehl `add-migration Initial` zum Erstellen der anfänglichen Migrations. Der Name "Initial" ist willkürlich und wird verwendet, um die Namen der Migrationsdatei erstellt.

![](adding-a-new-field/_static/image6.png)

Code First-Migrationen wird eine weitere Klassendatei in der *Migrationen* Ordner (mit dem Namen *{Datumsstempel}\_Initial.cs* ), und diese Klasse enthält Code, der das Schema der Datenbank erstellt. Der Dateiname für die Migration ist bereits mit einem Zeitstempel fest, um mit der Sortierung zu. Überprüfen Sie die *{Datumsstempel}\_Initial.cs* Datei enthält sie die Anweisungen zum Erstellen der `Movies` Tabelle für die Movie-Datenbank. Beim Aktualisieren der Datenbank in den Anweisungen unten, diese *{Datumsstempel}\_Initial.cs* Datei ausgeführt wird und das Datenbankschema zu erstellen. Die **Ausgangswert** Methode wird ausgeführt, um die Datenbank mit Testdaten aufzufüllen.

In der **-Paket-Manager-Konsole**, geben Sie den Befehl `update-database` zum Erstellen der Datenbank und Ausführen der `Seed` Methode.

![](adding-a-new-field/_static/image7.png)

Wenn Sie eine Fehlermeldung, der angibt erhalten, eine Tabelle ist bereits vorhanden und kann nicht erstellt werden, es ist wahrscheinlich, da Sie die Anwendung ausgeführt, nachdem Sie die Datenbank gelöscht und bevor Sie ausgeführt `update-database`. In diesem Fall löschen Sie die *Movies.mdf* -Datei erneut, und wiederholen Sie den `update-database` Befehl. Wenn Sie weiterhin eine Fehlermeldung erhalten, löschen Sie den Ordner "Migrations" und den Inhalt, und beginnen Sie mit den Anweisungen am oberen Rand dieser Seite (d.h. Löschen der *Movies.mdf* -Datei, und fahren Sie mit der Enable-Migrations). Wenn Sie weiterhin eine Fehlermeldung erhalten, öffnen Sie SQL Server-Objekt-Explorer, und entfernen Sie die Datenbank aus der Liste.

Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL. Die Seed-Daten werden angezeigt.

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a>Hinzufügen einer Rating-Eigenschaft zum Movie-Modell

Starten Sie durch Hinzufügen eines neuen `Rating` Eigenschaft, um die vorhandene `Movie` Klasse. Öffnen der *Models\Movie.cs* -Datei und fügen die `Rating` Eigenschaft dieser Art:

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

Die vollständige `Movie` -Klasse sieht wie im folgenden Code:

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

Erstellen Sie die Anwendung (STRG + UMSCHALT + B).

Da Sie ein neues Feld hinzugefügt haben die `Movie` -Klasse, müssen Sie auch die Bindung zu aktualisieren *Zulassungsliste* , damit diese neue Eigenschaft eingeschlossen werden. Update der `bind` Attribut für `Create` und `Edit` Aktionsmethoden anwenden, um das Einschließen der `Rating` Eigenschaft:

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

Sie müssen auch die Ansichtsvorlagen aktualisieren, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.

Öffnen der *\Views\Movies\Index.cshtml* -Datei und fügen eine `<th>Rating</th>` Spaltenüberschrift direkt nach der **Preis** Spalte. Fügen Sie dann eine `<td>` Spalte am Ende der Vorlage zum Rendern der `@item.Rating` Wert. Im folgenden finden Sie welche die aktualisierte *"Index.cshtml"* ansichtsvorlage sieht wie folgt aus:

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

Öffnen Sie als Nächstes die *\Views\Movies\Create.cshtml* -Datei und fügen die `Rating` Feld mit dem folgenden Markup der Aktivitätsbaum hervorgehoben. Dies rendert ein Textfeld, sodass Sie eine Bewertung angeben können, wenn Sie ein neuer Film erstellt wird.

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

Sie haben jetzt den Anwendungscode so, dass die Unterstützung der neuen `Rating` Eigenschaft.

Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL. Wenn Sie dies tun, wird jedoch eine der folgenden Fehlermeldungen angezeigt:

![](adding-a-new-field/_static/image9.png)  
  
Das Modell, das den Kontext 'MovieDBContext' unterstützt wurde geändert, da die Datenbank erstellt wurde. Erwägen Sie die Verwendung von Code First-Migrationen zum Aktualisieren der Datenbank (https://go.microsoft.com/fwlink/?LinkId=238269).

![](adding-a-new-field/_static/image10.png)

Sie sehen diesen Fehler, da die aktualisierte `Movie` Modellklasse, die in der Anwendung unterscheidet sich von dem Schema der der `Movie` Tabelle der vorhandenen Datenbank. (Die Datenbanktabelle enthält nicht die Spalte `Rating`.)


Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:

1. Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen. Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln. Der Nachteil ist jedoch, dass Sie in der Datenbank vorhandene Daten verloren gehen, damit Sie *nicht* dieser Ansatz in einer Produktionsdatenbank verwendet werden soll. Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung. Weitere Informationen zu Entity Framework-Datenbankinitialisierer, finden Sie unter [ASP.NET MVC/Entity Framework-Tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).
2. Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt. Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten. Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.
3. Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.


Für dieses Tutorial verwenden wir Code First-Migrationen.

Aktualisieren Sie die Seed-Methode, sodass sie einen Wert für die neue Spalte bereitstellt. Öffnen Sie migrations\configuration. cs-Datei, und fügen ein Bewertungsfeld jedes Movie-Objekt hinzu.

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

Erstellen Sie die Projektmappe, und öffnen Sie die **-Paket-Manager-Konsole** Fenster, und geben Sie den folgenden Befehl aus:

`add-migration Rating`

Die `add-migration` Befehl weist das migrationsframework an das aktuelle Movie-Modell mit dem aktuellen Film DB Schema überprüfen und den erforderlichen Code zum Migrieren der Datenbank in das neue Modell zu erstellen. Der Name *Bewertung* ist willkürlich und wird verwendet, um die Migrationsdatei zu benennen. Es ist hilfreich, einen aussagekräftigen Namen für den Migrationsschritt zu verwenden.

Wenn dieser Befehl abgeschlossen ist, handelt es sich bei Visual Studio öffnet die Klassendatei, die die neue definiert `DbMigration` abgeleitete Klasse sein, und klicken Sie in der `Up` Methode sehen Sie den Code, der die neue Spalte erstellt.

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

Erstellen Sie die Projektmappe, und geben Sie dann die `update-database` -Befehl in der **-Paket-Manager-Konsole** Fenster.

Die folgende Abbildung zeigt die Ausgabe in die **-Paket-Manager-Konsole** Fenster (der Stempel Datum voranstellen *Bewertung* unterscheiden.)

![](adding-a-new-field/_static/image11.png)

Führen Sie die Anwendung erneut aus, und navigieren Sie zu der URL /Movies. Sie sehen, dass das neue Feld für die Bewertung.

![](adding-a-new-field/_static/image12.png)

Klicken Sie auf die **neu erstellen** Link, um einen neuen Film hinzuzufügen. Beachten Sie, dass Sie eine Bewertung hinzufügen können.

![7_CreateRioII](adding-a-new-field/_static/image13.png)

Klicken Sie auf **Erstellen**. Der neue Film, einschließlich "Rating", wird jetzt in den Filmen auflisten:

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

Nun, da das Projekt Migrationen verwendet wird, müssen Sie nicht die Datenbank zu löschen, wenn Sie ein neues Feld hinzufügen oder aktualisieren Sie andernfalls das Schema. Im nächsten Abschnitt wir weitere schemaänderungen vornehmen und Migrationen zum Aktualisieren der Datenbank verwenden.

Sie sollten auch hinzufügen, die `Rating` Feld die Ansichtsvorlagen bearbeiten, Details und löschen.

Könnten Sie eingeben, den Befehl "Update-Database" in der **-Paket-Manager-Konsole** Fenster erneut und keinen Code für die Migration ausgeführt werden, da das Modell mit dem Schema übereinstimmt. Ausführen von "Update-Database" wird jedoch ausgeführt der `Seed` -Methode erneut auf, und wenn Sie die Seed-Daten geändert haben, die Änderungen verloren da die `Seed` Upserts Methodendaten. Erfahren Sie mehr über die `Seed` -Methode in der Tom Dykstra beliebte [ASP.NET MVC/Entity Framework-Tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).

In diesem Abschnitt wurde erläutert, wie Sie Modellobjekte ändern und die Datenbank mit den Änderungen synchron zu halten. Sie haben zudem eine Möglichkeit, eine neu erstellte Datenbank mit Beispieldaten aufzufüllen, damit Sie die Szenarien ausprobieren können. Dies war nur eine kurze Einführung in Code First, finden Sie unter [erstellen ein Entity Framework-Datenmodells für eine ASP.NET MVC-Anwendung](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) ein vollständiges Tutorial zu diesem Thema. Als Nächstes sehen wir uns, wie Sie die Modellklassen umfangreichere Validierungslogik hinzugefügt und einige Geschäftsregeln erzwungen werden zu aktivieren.

> [!div class="step-by-step"]
> [Zurück](adding-search.md)
> [Weiter](adding-validation.md)
