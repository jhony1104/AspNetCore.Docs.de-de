---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
title: 'Tutorial: Erste Schritte mit Entity Framework 6 Code First anhand von MVC 5 | Microsoft-Dokumentation'
description: In dieser Reihe von Tutorials erfahren Sie, wie zum Erstellen einer ASP.NET MVC 5-Anwendung, die Entity Framework 6 für den Datenzugriff verwendet.
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 00bc8b51-32ed-4fd3-9745-be4c2a9c1eaf
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: a00a5a3aa295c2584b90abbf931c258c9e1b58ca
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889950"
---
# <a name="tutorial-get-started-with-entity-framework-6-code-first-using-mvc-5"></a>Tutorial: Erste Schritte mit Entity Framework 6 Code First anhand von MVC 5

> [!NOTE]
> Für Neuentwicklungen empfohlen [ASP.NET Core Razor Pages](/aspnet/core/razor-pages) über ASP.NET MVC-Controller und Ansichten. Für eine Reihe von Tutorials etwa wie folgt mithilfe von Razor-Seiten finden Sie [Lernprogramm: Erste Schritte mit Razor Pages in ASP.NET Core](/aspnet/core/tutorials/razor-pages/razor-pages-start). Das neue Tutorial an:
> * Ist einfacher zu befolgen.
> * Bietet mehr bewährte Methoden für Entity Framework Core.
> * Verwendet effizientere Abfragen.
> * Passt besser zur aktuellen API.
> * Behandelt mehr Features.
> * Ist die bevorzugte Methode für die Entwicklung neuer Anwendungen.

In dieser Reihe von Tutorials erfahren Sie, wie zum Erstellen einer ASP.NET MVC 5-Anwendung, die Entity Framework 6 für den Datenzugriff verwendet. Dieses Tutorial verwendet die Code First-Workflow. Informationen zum Code First Database First- und Model First Auswahl finden Sie unter [ein Modell erstellen](/ef/ef6/modeling/).

Dieser tutorialreihe wird erläutert, wie die beispielanwendung der Contoso University. Die beispielanwendung ist eine einfache University-Website. Mithilfe dieser Option können Sie anzeigen und Aktualisieren von Studenten, Kursen und "Instructor" Informationen. Hier sind zwei der Bildschirme, die Sie erstellen:

![Students_Index_page](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image1.png)

![Kursteilnehmer bearbeiten](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image2.png)

In diesem Tutorial:

> [!div class="checklist"]
> * MVC Web app erstellen
> * Einrichten des Websitestils
> * Installieren von Entitätsframework 6
> * Erstellen des Datenmodells
> * Erstellen des Datenbankkontexts
> * Initialisieren der Datenbank mit Testdaten
> * Richten Sie EF 6, um LocalDB zu verwenden
> * Erstellen von Controller und Ansichten
> * Anzeigen der Datenbank

## <a name="prerequisites"></a>Vorraussetzungen

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

## <a name="create-an-mvc-web-app"></a>MVC Web app erstellen

1. Öffnen Sie Visual Studio, und erstellen Sie eine C# Web-Projekt mithilfe der **ASP.NET-Webanwendung ((.NET Framework)** Vorlage. Nennen Sie das Projekt *ContosoUniversity* , und wählen Sie **OK**.

   ![Im Dialogfeld Neues Projekt in Visual Studio](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-project-dialog.png)

1. In **neue ASP.NET-Webanwendung – ContosoUniversity**Option **MVC**.

   ![Neues Dialogfeld der Web-Anwendung in Visual Studio](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-web-app-dialog.png)

    > [!NOTE]
    > In der Standardeinstellung die **Authentifizierung** Option wird festgelegt, um **keine Authentifizierung**. In diesem Tutorial erforderlich keine Web-app-Benutzer anmelden. Es verhindert nicht, darüber hinaus den Zugriff auf Grundlage, wer angemeldet ist.

1. Klicken Sie auf **OK**, um das Projekt zu erstellen.

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.

1. Open *Views\Shared\\"_Layout.cshtml"*, und nehmen Sie die folgenden Änderungen:

   - Ändern Sie jedes Vorkommen von "My ASP.NET Application" und "Application Name" in "Contoso University".
   - Fügen Sie Menüeinträge für Schüler/Studenten, Kurse, Dozenten und Abteilungen, und löschen Sie den Eintrag wenden Sie sich an.

   Die Änderungen werden im folgenden Codeausschnitt hervorgehoben:

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample1.cshtml?highlight=6,19,24-27,38)]

2. In *Views\Home\Index.cshtml*, ersetzen Sie den Inhalt der Datei mit den folgenden Code hinzu, den Text zu ASP.NET und MVC durch Text zu dieser Anwendung zu ersetzen:

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample2.cshtml)]

3. Drücken Sie STRG + F5, um die Website aus. Die Startseite mit im Hauptmenü angezeigt.

## <a name="install-entity-framework-6"></a>Installieren von Entitätsframework 6

1. Aus der **Tools** Menü Wählen Sie **NuGet Paket-Manager**, und wählen Sie dann **Paket-Manager Konsole**.

2. In der **-Paket-Manager-Konsole** Fenster, geben Sie den folgenden Befehl aus:

   ```text
   Install-Package EntityFramework
   ```

Dieser Schritt ist eine der wenigen Schritten, die in diesem Lernprogramm Sie jetzt aber, hätte automatisch von ASP.NET MVC Gerüstbau Feature. Sie können manuell tun, damit die erforderlichen Schritte zum Verwenden von Entity Framework (EF) sehen. Sie müssen Gerüstbau später verwenden, um die MVC-Controller und Ansichten zu erstellen. Alternativ kann können Gerüstbau automatisch das EF-NuGet-Paket installieren, erstellen den Datenbankkontext-Klasse und die Verbindungszeichenfolge zu erstellen. Wenn Sie auf diese Weise dafür bereit sind, müssen Sie, lediglich diese Schritte überspringen und das Gerüst für Ihre MVC-Controller nach der Erstellung Ihrer Entitätsklassen.

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Als nächstes erstellen Sie Entitätsklassen für die Contoso University-Anwendung. Beginnen Sie mit dem folgenden drei Entitäten:

**Kurs** <-> **Registrierung** <-> **für Schüler und Studenten**

| Entitäten | Beziehung |
| -------- | ------------ |
| Kurs zur Registrierung | 1: n |
| "Student", um die Registrierung | 1: n |

Es besteht eine 1:n-Beziehung zwischen den Entitäten `Student` und `Enrollment`. Außerdem besteht eine 1:n-Beziehung zwischen den Entitäten `Course` und `Enrollment`. Das bedeutet, dass ein Student für beliebig viele Kurse angemeldet sein kann und sich für jeden Kurs eine beliebige Anzahl von Studenten anmelden kann.

In den folgenden Abschnitten erstellen Sie eine Klasse für jede diese Entitäten.

> [!NOTE]
> Wenn Sie versuchen, das Projekt zu kompilieren, vor dem Erstellen aller dieser Entitätsklassen, erhalten Sie Compilerfehler zu beheben.

### <a name="the-student-entity"></a>Die Entität „Student“

- In der *Modelle* Ordner Klassendatei erstellen *Student.cs* mit der rechten Maustaste auf den Ordner **Projektmappen-Explorer** und **hinzufügen**  >  **Klasse**. Ersetzen Sie den Vorlagencode durch den folgenden Code:

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample3.cs)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht. Standardmäßig interpretiert Entity Framework eine Eigenschaft mit dem Namen `ID` oder *Classname* `ID` als Primärschlüssel.

Die `Enrollments`-Eigenschaft ist eine *Navigationseigenschaft*. Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind. In diesem Fall die `Enrollments` Eigenschaft eine `Student` Entität enthält alle der `Enrollment` Entitäten, die im Zusammenhang mit, `Student` Entität. Das heißt, wenn eine angegebene `Student` Zeile in der Datenbank verfügt über zwei im Zusammenhang `Enrollment` Zeilen (Zeilen, die Primärschlüssel des Studenten enthalten Wert in ihre `StudentID` Fremdschlüsselspalte), die von `Student` Entität `Enrollments` Navigationseigenschaft Diese beiden `Enrollment` Entitäten.

Navigationseigenschaften werden in der Regel als definiert `virtual` , damit sie bestimmte Entity Framework-Funktionen wie z. B. nutzen können *Lazy Load*. (Lazy Loading werden erläutert werden weiter unten in der [Lesen von verwandten Daten](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) Tutorial weiter unten in dieser Reihe.)

Wenn eine Navigationseigenschaft mehrere Entitäten enthalten kann (wie bei m:n- oder 1:n-Beziehungen), muss dessen Typ aus einer Liste bestehen, in der Einträge hinzugefügt, gelöscht und aktualisiert werden können – z.B.: `ICollection`.

### <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

- Erstellen Sie im Ordner *Models* (Modelle) die Datei *Enrollment.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code:

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample4.cs)]

Die `EnrollmentID` Eigenschaft wird der Primärschlüssel sein; diese Entität verwendet die *Classname* `ID` -Muster anstelle von `ID` selbst, wie Sie gesehen haben, der `Student` Entität. Normalerweise würden Sie nur ein Muster auswählen und dieses für das gesamte Datenmodell verwenden. Diese Variation soll verdeutlichen, dass Sie ein beliebiges Muster erstellen können. Sehen Sie in einem späteren Tutorial, wie sich durch Verwendung `ID` ohne `classname` erleichtert es, die Vererbung in das Datenmodell zu implementieren.

Die `Grade` -Eigenschaft ist ein [Enum](/ef/ef6/modeling/code-first/data-types/enums). Das Fragezeichen nach der `Grade` Typdeklaration gibt an, dass die `Grade` -Eigenschaft ist [auf NULL festlegbare](/dotnet/csharp/programming-guide/nullable-types/using-nullable-types). Eine Grade-Eigenschaft, die null unterscheidet sich von einer 0 (null) auf Unternehmensniveau – Null bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch nicht zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft. Eine `Enrollment`-Entität wird einer `Student`-Entität zugeordnet, damit die Eigenschaft nur eine `Student`-Entität enthalten kann. Dies steht im Gegensatz zu der bereits erläuterten `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthalten kann.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und die zugehörige Navigationseigenschaft lautet `Course`. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entitätsframework interpretiert Eigenschaften als Fremdschlüsseleigenschaften, wenn sie den Namen *&lt;navigationseigenschaftennamen&gt;&lt;Primärschlüsseleigenschaft Namen&gt;* (z. B. `StudentID`für die `Student` Navigationseigenschaft, da die `Student` Primärschlüssel der Entität ist `ID`). Fremdschlüsseleigenschaften können auch einfach den Namen der gleiche *&lt;Primärschlüsseleigenschaft Namen&gt;* (z. B. `CourseID` seit der `Course` Primärschlüssel der Entität ist `CourseID`).

### <a name="the-course-entity"></a>Die Entität „Course“

- In der *Modelle* Ordner erstellen *Course.cs*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample5.cs)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Wir sagen mehr über das <xref:System.ComponentModel.DataAnnotations.Schema.DatabaseGeneratedAttribute> -Attribut in einem späteren Lernprogramm in dieser Serie. Im Grunde können Sie über dieses Attribut den Primärschlüssel für den Kurs angeben, anstatt ihn von der Datenbank generieren zu lassen.

## <a name="create-the-database-context"></a>Erstellen des Datenbankkontexts

Ist die Hauptklasse, die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert die *Datenbankkontext* Klasse. Erstellen Sie diese Klasse durch Ableiten von der [System.Data.Entity.DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx) Klasse. Im Code Geben Sie an, welche Entitäten im Datenmodell enthalten sind. Außerdem können Sie bestimmte Entity Framework-Verhalten anpassen. In diesem Projekt heißt die Klasse `SchoolContext`.

- Zum Erstellen eines Ordners im Projekt ContosoUniversity, Rechtsklick im Projekt im **Projektmappen-Explorer** , und klicken Sie auf **hinzufügen**, und klicken Sie dann auf **neuer Ordner**. Nennen Sie diesen Ordner *DAL* (für Data Access Layer). Erstellen Sie in diesem Ordner eine neue Klassendatei mit dem Namen *SchoolContext.cs*, und Ersetzen Sie den Code durch folgenden Code:

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample6.cs)]

### <a name="specify-entity-sets"></a>Entitätenmengen angeben

Dieser Code erstellt eine ["DbSet"](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.113).aspx) -Eigenschaft für jede Entitätenmenge. In der Terminologie von Entity Framework eine *Entitätenmenge* entspricht in der Regel einer Datenbanktabelle und ein *Entität* entspricht einer Zeile in der Tabelle.

> [!NOTE]
>
> Sie lassen die `DbSet<Enrollment>` und `DbSet<Course>` -Anweisungen und funktioniert identisch. Entity Framework zählen sie implizit da der `Student` Entitätsverweise der `Enrollment` Entität und die `Enrollment` Entitätsverweise der `Course` Entität.

### <a name="specify-the-connection-string"></a>Geben Sie die Verbindungszeichenfolge

Der Name der Verbindungszeichenfolge (die Sie der Datei "Web.config" später hinzufügen) wird in an den Konstruktor übergeben.

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=1)]

Sie könnten auch die Verbindungszeichenfolge selbst anstelle des Namens eines übergeben, die in der Datei "Web.config" gespeichert ist. Weitere Informationen über Optionen zum Angeben der Datenbank finden Sie unter [Verbindungszeichenfolgen und Modelle](/ef/ef6/fundamentals/configuring/connection-strings).

Wenn Sie eine Verbindungszeichenfolge oder den Namen eines nicht explizit angeben, wird das Entity Framework davon ausgegangen, dass der Name der Verbindungszeichenfolge den Namen der Klasse identisch ist. Die Namen der Standardverbindungszeichenfolge in diesem Beispiel werden dann `SchoolContext`, identisch mit, was Sie explizit angeben.

### <a name="specify-singular-table-names"></a>Einzigartige Tabellennamen angeben

Die `modelBuilder.Conventions.Remove` -Anweisung in der ["onmodelcreating"](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx) Methode verhindert, dass Tabellennamen im Plural wird. Wenn Sie dies nicht gemacht hätte, die generierten Tabellen in der Datenbank hieße `Students`, `Courses`, und `Enrollments`. Stattdessen werden die Tabellennamen `Student`, `Course`, und `Enrollment`. Entwickler sind sich uneinig darüber, ob Tabellennamen im Plural stehen sollten oder nicht. In diesem Tutorial wird die Form im singular verwendet, aber der wichtigste Punkt ist, dass Sie, welcher Form Sie bevorzugen auswählen können, durch Einfügen oder diese Zeile des Codes auslassen.

## <a name="initialize-db-with-test-data"></a>Initialisieren der Datenbank mit Testdaten

Entity Framework kann automatisch erstellt (oder drop erstellen und) eine Datenbank zur Ausführung die Anwendung. Sie können angeben, dass dies erfolgen sollte jedes Mal, wenn Ihre Anwendung ausgeführt wird, oder nur, wenn das Modell mit der vorhandenen Datenbank synchron sind. Sie können auch schreiben eine `Seed` Methode, Entity Framework ruft automatisch nach dem Erstellen der Datenbank, um sie mit Daten zu füllen.

Das Standardverhalten ist zum Erstellen einer Datenbank nur dann, wenn sie nicht vorhanden ist (und löst eine Ausnahme aus, wenn das Modell geändert wurde, und die Datenbank bereits vorhanden ist). In diesem Abschnitt geben Sie die Datenbank sollte gelöscht und neu erstellt, wenn das Modell ändert. Löschen der Datenbank bewirkt, dass den Verlust aller Daten. Dies ist im Allgemeinen gut während der Entwicklung, da die `Seed` Methode wird ausgeführt, wenn die Datenbank neu erstellt und erneut Testdaten erstellen. In der Produktion in der Regel möchten jedoch nicht alle Ihre Daten verloren gehen, jedes Mal, wenn Sie das Schema der Datenbank ändern müssen. Weiter unten sehen Sie, wie Sie die Änderungen des Datenmodells zu behandeln, indem Sie Code First-Migrationen zum Ändern des Datenbankschemas löschen und Neuerstellen der Datenbank.

1. Erstellen Sie eine neue Klassendatei im Ordner DAL *SchoolInitializer.cs* und ersetzen den Code durch den folgenden Code, der eine Datenbank bei Bedarf erstellt werden und lädt Daten in die neue Datenbank testen.

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample8.cs)]

   Die `Seed` Methode nimmt das Datenbank-Context-Objekt als Eingabeparameter und der Code in der Methode verwendet das Objekt auf der Datenbank neue Entitäten hinzugefügt. Für jeden Entitätstyp, der Code erstellt eine Auflistung von neuen Entitäten, fügt diese an die entsprechende `DbSet` Eigenschaft, und klicken Sie dann speichert die Änderungen an der Datenbank. Es ist nicht notwendig, die `SaveChanges` Methode nach jeder Gruppe von Entitäten, wie hier geschehen ist, aber dies hilft Ihnen, die Ursache eines Problems zu suchen, wenn eine Ausnahme auftritt, während der Code in die Datenbank geschrieben werden.

2. Um sagen Entity Framework, die Ihre Initialisiererklasse verwenden, fügen Sie ein Element der `entityFramework` Element in der Anwendung *"Web.config"* (die eine Datei in den Stammordner des Projekts), wie im folgenden Beispiel gezeigt:

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample9.xml?highlight=2-6)]

   Die `context type` gibt an, der Name der vollqualifizierte Kontext und die Assembly, die er sich befindet, können und die `databaseinitializer type` gibt an, den vollqualifizierten Namen der Initialisiererklasse und die Assembly ist. (Wenn Sie nicht, dass EF die Initialisierer verwenden möchten, können Sie ein Attribut festlegen, auf die `context` Element: `disableDatabaseInitialization="true"`.) Weitere Informationen finden Sie unter [Configuration File Settings](/ef/ef6/fundamentals/configuring/config-file).

   Als Alternative zum Einstellen der Initialisierung der *Web.config* Datei ist im Code vornehmen, indem eine `Database.SetInitializer` Anweisung die `Application_Start` Methode in der *Global.asax.cs* Datei. Weitere Informationen finden Sie unter [Grundlegendes zu Datenbank-Initialisierer in Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm).

Die Anwendung ist nun so eingerichtet, dass Entity Framework die Datenbank das Modell, wenn Sie die Datenbank zum ersten Mal in einer bestimmten Anwendung vergleicht (Ihre `SchoolContext` und Entitätsklassen). Wenn es ein Unterschied besteht, wird von die Anwendung gelöscht und neu erstellt die Datenbank.

> [!NOTE]
> Wenn Sie eine Anwendung auf einem Webserver für die Produktion bereitstellen, müssen Sie entfernen oder Deaktivieren von Code, der gelöscht und die Datenbank neu erstellt. Haben Sie Gelegenheit, die in einem späteren Tutorial dieser Reihe.

## <a name="set-up-ef-6-to-use-localdb"></a>Richten Sie EF 6, um LocalDB zu verwenden

[LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb?view=sql-server-2017) ist eine einfache Version von SQL Server Express-Datenbankmoduls. Es ist einfach zu installieren und konfigurieren, wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt wird. LocalDB ausgeführt wird, in einem speziellen Ausführungsmodus von SQL Server Express, die Ihnen ermöglicht, die Arbeit mit Datenbanken als *mdf* Dateien. Sie können LocalDB-Datenbankdateien abzulegen, der *App\_Daten* Ordner eines Webprojekts, wenn die Datenbank mit dem Projekt kopiert werden sollen. Das Feature "Instanz" in SQL Server Express ermöglicht Ihnen die Arbeit mit auch *mdf* Dateien, aber das Feature "Instanz" ist veraltet; LocalDB wird daher empfohlen, für die Arbeit mit *mdf* Dateien. LocalDB ist standardmäßig mit Visual Studio installiert.

SQL Server Express verwendet in der Regel nicht für die Produktion von ASP.NET-Webanwendungen. LocalDB ist insbesondere für die Produktion mit einer Web-Anwendung abgeraten, da es nicht ausgelegt wurde mit IIS.

- In diesem Lernprogramm arbeiten Sie mit LocalDB. Öffnen Sie die Anwendung *"Web.config"* -Datei und fügen eine `connectionStrings` Element vor dem `appSettings` Element, wie im folgenden Beispiel dargestellt. (Stellen Sie sicher, dass Sie aktualisieren die *"Web.config"* -Datei in den Stammordner des Projekts. Gibt es auch ein *Web.config* in der Datei der *Ansichten* Unterordner, die Sie nicht aktualisieren möchten.)

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample10.xml?highlight=1-3)]

Die Verbindungszeichenfolge, die Sie hinzugefügt haben gibt an, dass Entity Framework eine LocalDB-Datenbank, die mit dem Namen verwendet *ContosoUniversity1.mdf*. (Noch nicht die Datenbank existiert aber EF erstellen.) Möchten Sie die Datenbank erstellt die *App\_Daten* Ordner konnte hinzufügen `AttachDBFilename=|DataDirectory|\ContosoUniversity1.mdf` der Verbindungszeichenfolge. Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [SQL Server-Verbindungszeichenfolgen für ASP.NET-Webanwendungen](/previous-versions/aspnet/jj653752(v=vs.110)).

Sie nicht unbedingt eine Verbindungszeichenfolge in der *Web.config* Datei. Wenn Sie eine Verbindungszeichenfolge angeben, verwendet Entity Framework eine Standardverbindungszeichenfolge basierend auf der Datenkontextklasse. Weitere Informationen finden Sie unter [Code First in eine neue Datenbank](/ef/ef6/modeling/code-first/workflows/new-database).

## <a name="create-controller-and-views"></a>Erstellen von Controller und Ansichten

Jetzt erstellen Sie eine Webseite zum Anzeigen von Daten. Der Prozess der angeforderten Daten automatisch löst die Erstellung der Datenbank. Sie beginnen, indem Sie einen neuen Domänencontroller erstellen. Aber bevor Sie dies tun, erstellen Sie das Projekt aus, um die Klassen Modell und Kontext auf MVC-Controller-Gerüstbau verfügbar zu machen.

1. Mit der rechten Maustaste die **Controller** Ordner **Projektmappen-Explorer**Option **hinzufügen**, und klicken Sie dann auf **neues Gerüstelement**.
2. In der **Gerüst hinzufügen** Dialogfeld **MVC 5-Controller mit Ansichten mit Entity Framework**, und wählen Sie dann **hinzufügen**.

     ![Fügen Sie Scaffold-Dialogfeld hinzu "" in Visual Studio](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

3. In der **Controller hinzufügen** die folgenden Optionen, und wählen Sie dann **hinzufügen**:

   - Modellklasse: **Für Schüler und Studenten (ContosoUniversity.Models)**. (Wenn Sie diese Option in der Dropdown-Liste nicht angezeigt wird, erstellen Sie das Projekt, und versuchen Sie es erneut.)
   - Datenkontextklasse: **SchoolContext (ContosoUniversity.DAL)**.
   - Name des Controllers: **StudentController** (nicht StudentsController).
   - Übernehmen Sie die Standardwerte für die anderen Felder aus.

     Beim Klicken auf **hinzufügen**, der gerüstbauer erstellt eine *StudentController.cs* -Datei und eine Reihe von Ansichten (*cshtml* Dateien), die mit dem Controller arbeiten. Zukünftig Wenn Sie Projekte erstellen, die Entity Framework verwenden, Sie können auch nutzen einige zusätzliche Funktionen der gerüstbauer: Erstellen Sie Ihre erste Modellklasse, eine Verbindungszeichenfolge erstellen und dann in das **Controller hinzufügen** angeben **neuen Datenkontext** durch Auswählen der **+** neben **-Klasse**. Der gerüstbauer erstellt Ihre `DbContext` string-Klasse und Ihre Verbindung sowie die Controller und Ansichten.
4. Visual Studio öffnet die *Controllers\StudentController.cs* Datei. Sie sehen, dass eine Klassenvariable erstellt wurde, die ein Datenbankobjekt Kontext instanziiert:

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

     Die `Index` Action-Methode ruft eine Liste von Schülern und Studenten die *Schüler/Studenten* Entitätenmenge durch Lesen der `Students` Eigenschaft des Context-Instanz der Datenbank:

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

     Die *Student\Index.cshtml* zeigt diese Liste in einer Tabelle:

     [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample13.cshtml)]
5. Drücken Sie STRG + F5, um das Projekt auszuführen. (Wenn Sie eine Fehlermeldung "Der Schattenkopie kann nicht erstellt werden" erhalten, schließen Sie den Browser, und versuchen Sie es erneut.)

     Klicken Sie auf die **Schüler/Studenten** Tab, um die Testdaten abzurufen, die die `Seed` -Methode eingefügt wurden. Je nachdem, wie eng ist das Browserfenster, sehen Sie den Link "Student"-Registerkarte in der Top-Adressleiste ein, oder müssen Sie auf der oberen rechten Ecke, um den Link finden Sie unter.

     ![Menütaste](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image14.png)

## <a name="view-the-database"></a>Anzeigen der Datenbank

Wenn Sie Kursteilnehmer-Seite und die Anwendung versucht, auf die Datenbank zugreifen, entdeckt EF, es wurde keine Datenbank und erstellt. EF wurde die Seed-Methode, um die Datenbank mit Daten füllen.

Verwenden Sie entweder **Server-Explorer** oder **Objekt-Explorer von SQL Server** (SSOX), um die Datenbank in Visual Studio anzuzeigen. In diesem Lernprogramm verwenden Sie **Server-Explorer**.

1. Schließen Sie den Browser.
2. In **Server-Explorer**, erweitern **Datenquellen** (Sie müssen zuerst auf die Aktualisierungsschaltfläche), erweitern Sie **Schulkontext (ContosoUniversity)**, und erweitern Sie dann  **Tabellen** Tabellen in die neue Datenbank an.

3. Mit der rechten Maustaste die **für Schüler und Studenten** Tabelle, und klicken Sie auf **Tabellendaten anzeigen** , finden Sie unter den Spalten, die erstellt wurden und die Zeilen, die in die Tabelle eingefügt wurden.

4. Schließen der **Server-Explorer** Verbindung.

Die *ContosoUniversity1.mdf* und *ldf* -Datenbankdateien befinden sich in der *% USERPROFILE%* Ordner.

Da Sie verwenden die `DropCreateDatabaseIfModelChanges` Initialisierer, können Sie nun eine Änderung vornehmen der `Student` Klasse, die Anwendung erneut auszuführen und die Datenbank automatisch Ihren Änderungen entsprechend neu erstellt werden würde. Angenommen, Sie fügen ein `EmailAddress` -Eigenschaft der `Student` Klasse Kursteilnehmer Seite erneut ausführen, und sehen Sie dann die Tabelle erneut, sehen Sie eine neue `EmailAddress` Spalte.

## <a name="conventions"></a>Konventionen

Der Code voraussetzten Entität Rahmen eine vollständige Datenbank erstellen können ist aufgrund der *Konventionen*, oder Entity Framework macht Annahmen. Einige von ihnen haben bereits erwähnt wurde, oder verwendet wurden, ohne Ihre kennen:

- Die pluralisierte Formen der Entity-Klassennamen werden als Tabellennamen verwendet.
- Eigenschaftennamen von Entitäten werden als Spaltennamen verwendet.
- Eigenschaften der Entität mit dem Namen `ID` oder *Classname* `ID` werden als Primärschlüsseleigenschaften erkannt.
- Eine Eigenschaft wird als Fremdschlüsseleigenschaften interpretiert, wenn sie den Namen *&lt;navigationseigenschaftennamen&gt;&lt;Primärschlüsseleigenschaft Namen&gt;* (z. B. `StudentID` für die `Student` Navigationseigenschaft, da die `Student` Primärschlüssel der Entität ist `ID`). Fremdschlüsseleigenschaften können auch einfach den Namen der gleiche &lt;Primärschlüsseleigenschaft Namen&gt; (z. B. `EnrollmentID` seit der `Enrollment` Primärschlüssel der Entität ist `EnrollmentID`).

Sie haben gesehen, dass die Konventionen überschrieben werden können. Beispielsweise angegeben werden kann, dass Tabellennamen im Plural werden darf nicht aus, und Sie später feststellen, wie Sie eine Eigenschaft als eine Fremdschlüsseleigenschaft explizit zu kennzeichnen.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu EF 6 finden Sie in diesen Artikeln:

* [ASP.NET: Datenzugriff – Empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md)

* [Code First-Konventionen](/ef/ef6/modeling/code-first/conventions/built-in)

* [Erstellen eines komplexeren Datenmodells](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellt eine MVC-Web-app
> * Einrichten des Websitestils
> * Installierte Entitätsframework 6
> * Erstellt das Datenmodell
> * Den Datenbankkontext erstellt
> * Initialisiert die Datenbank mit Testdaten
> * Richten Sie EF 6, um LocalDB zu verwenden
> * Erstellte Controller und Ansichten
> * Die Datenbank angezeigt

Wechseln Sie zum nächsten Artikel erfahren, wie Sie überprüfen und anpassen, erstellen, lesen, aktualisieren, löschen (CRUD) Code in Ihre Controller und Ansichten.
> [!div class="nextstepaction"]
> [Implementieren von grundlegenden CRUD-Funktionen](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)