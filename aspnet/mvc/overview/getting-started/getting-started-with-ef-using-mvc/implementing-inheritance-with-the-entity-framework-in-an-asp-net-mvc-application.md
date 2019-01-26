---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Vorlage: Implementieren von Vererbung mit EF in einer ASP.NET MVC 5-appS'
description: In diesem Tutorial erfahren Sie, wie Sie die Vererbung in das Datenmodell implementieren können.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 79513edce7ac3044f6f547149400cba7d307edfa
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889894"
---
# <a name="template-implement-inheritance-with-ef-in-an-aspnet-mvc-5-app"></a>Vorlage: Implementieren von Vererbung mit EF in einer ASP.NET MVC 5-app

Im vorherigen Tutorial haben Sie Parallelitätsausnahmen behandelt. In diesem Tutorial erfahren Sie, wie Sie die Vererbung in das Datenmodell implementieren können.

In der objektorientierten Programmierung können Sie [Vererbung](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) zur Erleichterung [Wiederverwendung von code](http://en.wikipedia.org/wiki/Code_reuse). In diesem Tutorial ändern Sie die Klassen `Instructor` und `Student` so, dass sie von einer `Person`-Basisklasse abgeleitet werden, die Eigenschaften wie `LastName` enthält. Diese Eigenschaften sind für Dozenten und Studenten gängig. Sie fügen keine Webseiten hinzu oder ändern diese, aber Sie werden Teile des Codes ändern. Diese Änderungen werden automatisch in der Datenbank widergespiegelt.

In diesem Tutorial:

> [!div class="checklist"]
> * Informationen Sie zum Zuordnen von Vererbung mit Datenbank
> * Erstellen der Klasse „Person“
> * Update "Instructor" und "Student"
> * Person zum Modell hinzuzufügen.
> * Erstellen und Aktualisieren von Migrationen
> * Testen der Implementierung
> * Bereitstellung in Azure

## <a name="prerequisites"></a>Vorraussetzungen

* [Implementieren von Vererbung](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="map-inheritance-to-database"></a>Zuordnen von Vererbung mit Datenbank

Die `Instructor` und `Student` Klassen in der `School` Datenmodell besitzt mehrere Eigenschaften, die identisch sind:

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

Angenommen, Sie möchten den redundanten Code für die Eigenschaften löschen, die von den Entitäten `Instructor` und `Student` gemeinsam genutzt werden. Oder Sie möchten einen Dienst schreiben, mit dem Namen formatiert werden können, ohne dass es eine Rolle spielt, ob der Name von einem Dozenten oder von einem Studenten stammt. Sie erstellen eine `Person` Basisklasse, die nur diese gemeinsam Eigenschaften genutzten enthält, muss sich die `Instructor` und `Student` Entitäten aus dieser Basisklasse erben, wie in der folgenden Abbildung gezeigt:

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

Es gibt mehrere Möglichkeiten, wie diese Vererbungsstruktur in der Datenbank dargestellt werden kann. Sie verfügen über eine `Person` Tabelle Informationen über Schüler/Studenten und Dozenten in einer einzelnen Tabelle enthält. Einige Spalten können nur für Dozenten anwenden (`HireDate`), einige nur für Studenten (`EnrollmentDate`), werden einige auf beide (`LastName`, `FirstName`). In der Regel müssen eine *Diskriminator* Spalte, um anzugeben, welchen Typ von jeder Zeile darstellt. So kann die Unterscheidungsspalte beispielsweise „Instructor“ für Dozenten und „Student“ für Studenten enthalten.

![Tabelle-pro-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Dieses Muster zum Generieren von eine Vererbungsstruktur für Entitäten aus einer einzelnen Datenbanktabelle heißt *Tabelle pro Hierarchie* (TPH)-Vererbung.

Alternativ kann die Datenbank so gestaltet werden, dass sie mehr wie die Vererbungsstruktur aussieht. Angenommen, Sie verfügen über nur die Felder der `Person` Tabelle, und über separate `Instructor` und `Student` Tabellen mit den Feldern.

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Dieses Muster eine Datenbanktabelle zu machen, für jede Entitätsklasse aufgerufen *Tabelle pro Typ* (TPT)-Vererbung.

Eine weitere Möglichkeit besteht darin, individuellen Tabellen alle nicht abstrakten Typen zuzuordnen. Alle Eigenschaften einer Klasse, einschließlich der geerbten Eigenschaften, werden Spalten der entsprechenden Tabelle zugeordnet. Dieses Muster wird als TPC-Vererbung (TPC = Table per Concrete, Tabelle pro konkretem Typ) bezeichnet. Wenn Sie die TPC-Vererbung für implementiert die `Person`, `Student`, und `Instructor` Klassen wie zuvor gezeigt die `Student` und `Instructor` Tabellen sieht nach der zuvor Implementierung unterscheidet.

Bei den TPC- und TPH-vererbungsmustern bieten im Entity Framework als TPT-vererbungsmustern, in der Regel eine bessere Leistung, da TPT-Muster zu komplexen joinabfrage führen können.

Dieses Tutorial veranschaulicht die Implementierung der TPH-Vererbung. TPH ist das Standardmuster für die Vererbung im Entity Framework, daher müssen Sie lediglich erstellen eine `Person` Klasse, Ändern der `Instructor` und `Student` Klassen abgeleitet `Person`, fügen Sie die neue Klasse, die `DbContext`, und erstellen Sie eine die Migration. (Informationen dazu, wie Sie das andere Muster zu implementieren, finden Sie unter [Zuordnen der "Tabelle pro Typ (TPT)-Vererbung](https://msdn.microsoft.com/data/jj591617#2.5) und [Zuordnen der Tabelle pro konkreter Klasse (TPC)-Vererbung](https://msdn.microsoft.com/data/jj591617#2.6) in der MSDN-Website Entity Framework-Dokumentation.)

## <a name="create-the-person-class"></a>Erstellen der Klasse „Person“

In der *Modelle* Ordner erstellen *Person.cs* , und Ersetzen Sie den Vorlagencode durch den folgenden Code:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="update-instructor-and-student"></a>Update "Instructor" und "Student"

Jetzt aktualisieren, die *Instructor.cs* und *Sudent.cs* erben von Werten aus der *Person.sc*.

In *Instructor.cs*, leiten die `Instructor` -Klasse aus der `Person` Klasse, und entfernen Sie die Schlüssel- und Namensfelder. Der Code sieht aus wie im folgenden Beispiel:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Nehmen Sie ähnliche Änderungen an *Student.cs*. Die `Student` Klasse wird wie im folgenden Beispiel aussehen:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-person-to-the-model"></a>Person zum Modell hinzuzufügen.

In *SchoolContext.cs*, Hinzufügen einer `DbSet` -Eigenschaft für die `Person` Entitätstyp:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Das ist alles, was Entity Framework für die Konfiguration der „Tabelle pro Hierarchie“-Vererbung benötigt. Wie Sie sehen werden, wenn die Datenbank aktualisiert wird, müssen sie eine `Person` anstelle der Tabelle die `Student` und `Instructor` Tabellen.

## <a name="create-and-update-migrations"></a>Erstellen und Aktualisieren von Migrationen

Geben Sie in der Paket-Manager-Konsole (PMC) den folgenden Befehl ein:

`Add-Migration Inheritance`

Führen Sie die `Update-Database` Befehl in der PMC. An diesem Punkt schlägt der Befehl fehl, da haben wir die vorhandene Daten, die Migrationen nicht, wie behandeln. Sie erhalten eine Fehlermeldung wie folgt:

> *Objekt konnte nicht gelöscht werden "Dbo. "Instructor" ", da es durch eine FOREIGN KEY-Einschränkung verwiesen wird.*


Open *Migrationen\&Lt; Zeitstempel&gt;\_Inheritance.cs* , und Ersetzen Sie die `Up` Methode durch den folgenden Code:

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

Dieser Code übernimmt folgende Tasks für Datenbankaktualisierungen:

- Er entfernt Fremdschlüsseleinschränkungen und -indizes, die auf die Tabelle „Student“ verweisen.
- Er benennt die Tabelle „Instructor“ in „Person“ um und nimmt die Änderungen vor, die für das Speichern von Studentendaten erforderlich sind:

    - Er fügt das EnrollmentDate für Studenten hinzu, bei dem NULL-Werte zugelassen sind.
    - Er fügt eine Unterscheidungsspalte hinzu, um anzugeben, ob eine Zeile für einen Studenten oder für einen Dozenten bestimmt ist.
    - Er legt fest, dass bei HireDate NULL-Werte zugelassen sind, da die Zeilen für Studenten keine Einstellungsdaten enthalten.
    - Er fügt ein temporäres Feld hinzu, über das Fremdschlüssel aktualisiert werden sollen, die auf Studenten verweisen. Wenn Sie Studenten in der Person-Tabelle kopieren, erhalten sie neue Primärschlüsselwerte.
- Kopiert Daten aus der Tabelle „Student“ in die Tabelle „Person“. Dadurch werden Studenten neue Primärschlüsselwerte zugewiesen.
- Er legt Fremdschlüsselwerte fest, die auf Studenten verweisen.
- Er erstellt Fremdschlüsseleinschränkungen und -indizes neu, die dann auf die Tabelle „Person“ verweisen.

(Hätten Sie als Primärschlüsseltyp statt einem Integer die grafische Benutzeroberfläche verwendet haben, hätten die Primärschlüsselwerte für Studenten nicht geändert werden müssen, und mehrere dieser Schritte hätten ausgelassen werden können.)

Führen Sie die `update-database` -Befehl erneut aus.

(In einem Produktionssystem sollten Sie entsprechende Änderungen an der Down-Methode vornehmen für den Fall, dass Sie jemals verwenden müssten, um um zur vorherigen Datenbankversion zurückzukehren. In diesem Tutorial wird nicht die Down-Methode verwenden Sie.)

> [!NOTE]
> Es ist möglich, dass andere Fehler auftreten, bei der Migration von Daten und zu Änderungen am Datenbankschema. Wenn Sie Migrationsfehler erhalten Sie kann nicht aufgelöst werden, Sie können mit dem Tutorial fortfahren, durch Ändern der Verbindungszeichenfolge in der *"Web.config"* Datei oder durch Löschen der Datenbank. Der einfachste Ansatz ist zum Umbenennen der Datenbank in der *"Web.config"* Datei. Ändern Sie z. B. der Name der Datenbank in "contosouniversity2" ein, wie im folgenden Beispiel gezeigt:
>
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
>
> Mit einer neuen Datenbank, sind keine Daten zu migrieren, und die `update-database` Befehl ist sehr wahrscheinlich ohne Fehler abgeschlossen. Anweisungen dazu, wie Sie die Datenbank zu löschen, finden Sie unter [Gewusst wie: Löschen einer Datenbank in Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/). Wenn Sie diesen Ansatz verwenden, um dieses Lernprogramm fortsetzen, überspringen Sie den Bereitstellungsschritt am Ende dieses Tutorials bereitstellen Sie oder in eine neue Website und die Datenbank. Wenn Sie ein Update für denselben Standort, die, denen Sie bereits bereitstellen die Bereitstellung wurde haben, erhalten EF es den gleichen Fehler, wenn sie Migrationen automatisch ausgeführt wird. Wenn Sie einen Migrationen Fehler beheben möchten, ist die beste Ressource ein Entity Framework-Foren oder StackOverflow.com.

## <a name="test-the-implementation"></a>Testen der Implementierung

Führen Sie die Website, und Testen Sie verschiedene Seiten. Alles funktioniert genauso wie vorher.

In **Server-Explorer** erweitern **Daten Connections\SchoolContext** und dann **Tabellen**, und Sie sehen, dass die **für Schüler und Studenten** und **"Instructor"** Tabellen wurden durch ersetzt eine **Person** Tabelle. Erweitern Sie die **Person** Tabelle, und Sie finden Sie, dass sie alle Spalten, die verwendet werden hat, in der **für Schüler und Studenten** und **"Instructor"** Tabellen.

Klicken Sie mit der rechten Maustaste auf die Tabelle „Person“, und klicken Sie anschließend auf **Tabellendaten anzeigen**, um die Unterscheidungsspalte anzuzeigen.

Das folgende Diagramm veranschaulicht die Struktur der neuen Datenbank "School":

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a>Bereitstellung in Azure

In diesem Abschnitt müssen Sie das optionale haben **Bereitstellen der app für Azure** im Abschnitt [Teil 3, sortieren, Filtern und Paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) dieser tutorialreihe. Wenn Sie Migrationen Fehler, die Sie haben durch Löschen der Datenbank in Ihrem lokalen Projekt aufgelöst, überspringen Sie diesen Schritt; oder erstellen Sie eine neue Website und die Datenbank und in der neuen Umgebung bereitstellen.

1. In Visual Studio mit der Maustaste des Projekts im **Projektmappen-Explorer** , und wählen Sie **veröffentlichen** aus dem Kontextmenü.

2. Klicken Sie auf **Veröffentlichen**.

    Die Web-app wird im Standardbrowser geöffnet.

3. Testen Sie die Anwendung, überprüfen Sie, ob es funktioniert.

    Beim ersten einer Seite ausführen, auf die Datenbank zugreift, Entity Framework führt alle Migrationen `Up` Methoden erforderlich, um die Datenbank mit dem aktuellen Datenmodell auf dem neuesten Stand zu bringen.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie in der [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

Weitere Informationen zu diesen und anderen Strukturen Vererbung finden Sie unter [TPT-Vererbungsmuster](https://msdn.microsoft.com/data/jj618293) und [TPH-Vererbungsmuster](https://msdn.microsoft.com/data/jj618292) auf MSDN. Im nächsten Tutorial erfahren Sie, wie Sie eine Vielzahl von Entity Framework-Szenarios auf fortgeschrittenem Niveau verarbeiten können.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Gelernt, wie Sie die Vererbung Datenbank zuordnen
> * Erstellt die Person-Klasse
> * Aktualisierte "Instructor" und "Student"
> * Hinzugefügten Person auf das Modell
> * Erstellt, und Aktualisieren von Migrationen
> * Die Implementierung getestet
> * In Azure bereitgestellt

Wechseln Sie zum nächsten Artikel erfahren Sie mehr zu Themen, die zu beachten, wenn Sie die Grundlagen der Entwicklung von ASP.NET Web-Anwendungen, die Entity Framework Code First verwenden hinausgehen hilfreich sind.
> [!div class="nextstepaction"]
> [Erweiterte Entity Framework-Szenarien](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)