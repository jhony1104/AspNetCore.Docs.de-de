---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: Abrufen und Anzeigen von Daten mit model-Bindung und Web Forms | Microsoft-Dokumentation
author: Rick-Anderson
description: Diese tutorialreihe veranschaulicht die grundlegenden Aspekte der Verwendung von modellbindung mit einem ASP.NET Web Forms-Projekt. Modellbindung macht die dateninteraktion Weitere gerade-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: c53c27f4852eab9813bd917315111e7cd3b04953
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396284"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>Abrufen und Anzeigen von Daten mit modellbindung und Web forms
====================

> Diese tutorialreihe veranschaulicht die grundlegenden Aspekte der Verwendung von modellbindung mit einem ASP.NET Web Forms-Projekt. Modellbindung, wird die dateninteraktion mehr geradlinigere als Umgang mit Data Source-Objekte (z. B. "ObjectDataSource" oder SqlDataSource-Steuerelement). Diese Serie beginnt mit einführendes Material und verschiebt in späteren Tutorials zu erweiterten Konzepten übergegangen.
> 
>  Der Modell-bindungsmuster funktioniert mit jedem datenzugriffstechnologie. In diesem Tutorial verwenden Sie Entity Framework, jedoch können Sie die neue datenzugriffstechnologie, die Sie bereits bekannt ist. Von einem datengebundenen Serversteuerelement, z. B. eine GridView, ListView, DetailsView oder FormView-Steuerelement geben Sie den Namen der Methoden zum auswählen, aktualisieren, löschen und Erstellen von Daten verwendet. In diesem Tutorial werden Sie einen Wert für die SelectMethod angeben. 
> 
> In dieser Methode geben Sie die Logik zum Abrufen der Daten. Im nächsten Tutorial legen Sie Werte für UpdateMethod, DeleteMethod und InsertMethod fest.
>
> Sie können [herunterladen](https://go.microsoft.com/fwlink/?LinkId=286116) in das vollständige Projekt C# oder Visual Basic. Der herunterladbare Code funktioniert mit Visual Studio 2012 und höher. Er verwendet die Visual Studio 2012-Vorlage, die unterscheidet sich etwas von der Visual Studio 2017-Vorlage, die in diesem Tutorial gezeigt wird.
> 
> In diesem Tutorial führen Sie die Anwendung in Visual Studio. Sie können auch Bereitstellen der Anwendung bei einem Hostinganbieter und über das Internet verfügbar machen. Microsoft bietet kostenloses Webhosting für bis zu 10 Websites in einem  
>  [Kostenloses Azure-Testkonto](https://azure.microsoft.com/free/?WT.mc_id=A443DD604). Informationen dazu, wie Sie ein Visual Studio-Webprojekt für Azure App Service-Web-Apps bereitstellen, finden Sie unter den [ASP.NET-webbereitstellung mithilfe von Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) Reihe. In diesem Tutorial wird gezeigt, wie Entity Framework Code First-Migrationen zu verwenden, um Ihre SQL Server-Datenbank in Azure SQL-Datenbank bereitzustellen.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
> 
> - Microsoft Visual Studio 2017 "oder" Microsoft Visual Studio Community 2017
>   
> In diesem Tutorial funktioniert auch mit Visual Studio 2012 und Visual Studio 2013, aber es gibt einige Unterschiede in der Vorlage "Benutzer"-Schnittstelle und Projekt.


## <a name="what-youll-build"></a>Sie lernen Folgendes

In diesem Tutorial müssen Sie folgende Aktionen ausführen:

* Erstellen Sie Datenobjekte, die eine Universität mit Schüler/Studenten, die Lehrveranstaltungen widerspiegeln.
* Erstellen von Tabellen aus den Objekten
* Auffüllen der Datenbank mit Testdaten
* Anzeigen von Daten in einem Web form

## <a name="create-the-project"></a>Erstellen eines Projekts

1. Erstellen Sie in Visual Studio 2017 eine **ASP.NET-Webanwendung ((.NET Framework)** -Projekt namens **ContosoUniversityModelBinding**.

   ![Projekt erstellen](retrieving-data/_static/image19.png)

2. Klicken Sie auf **OK**. Das Dialogfeld zum Auswählen einer Vorlage wird angezeigt.

   ![Wählen Sie die Web forms](retrieving-data/_static/image3.png)

3. Wählen Sie die **Web Forms** Vorlage. 

4. Ändern Sie die Authentifizierung, bei Bedarf **einzelne Benutzerkonten**. 

5. Klicken Sie auf **OK**, um das Projekt zu erstellen.

## <a name="modify-site-appearance"></a>Erscheinungsbild der Website geändert werden.

   Stellen Sie einige Änderungen an den Standort Darstellung anpassen. 
   
   1. Öffnen Sie die Datei Site.Master.
   
   2. Ändern Sie den Titel anzuzeigende **Contoso University** und nicht **My ASP.NET Application**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. Ändern Sie den Headertext von **Anwendungsname** zu **Contoso University**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. Ändern Sie die Header-Navigationslinks, auf die entsprechende anpassen. 
   
      Entfernen Sie die Links, um **zu** und **wenden Sie sich an** und verknüpfen Sie stattdessen mit einem **Schüler/Studenten** Seite, die Sie erstellen möchten.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. Speichern Sie Site.Master.

## <a name="add-a-web-form-to-display-student-data"></a>Fügen Sie ein Webformular zum Anzeigen der Studentendaten

   1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, wählen Sie **hinzufügen** und dann **neues Element**. 
   
   2. In der **neues Element hinzufügen** wählen Sie im Dialogfeld die **Webformular mit Gestaltungsvorlage** Vorlage und nennen Sie sie **Students.aspx**.

      ![Seite erstellen](retrieving-data/_static/image5.png)

   3. Wählen Sie **Hinzufügen** aus.
   
   4. Wählen Sie für das Web Form die Masterseite **Site.Master**.
   
   5. Klicken Sie auf **OK**.
   

## <a name="add-the-data-model"></a>Datenmodell hinzufügen

In der **Modelle** Ordner, fügen Sie eine Klasse, die mit dem Namen **UniversityModels.cs**.

   1. Mit der rechten Maustaste **Modelle**Option **hinzufügen**, und klicken Sie dann **neues Element**. Das Dialogfeld **Neues Element hinzufügen** wird angezeigt.

   2. Wählen Sie im linken Navigationsmenü im **Code**, klicken Sie dann **Klasse**.

      ![Erstellen von Model-Klasse](retrieving-data/_static/image20.png)

   3. Nennen Sie die Klasse **UniversityModels.cs** , und wählen Sie **hinzufügen**.

      In dieser Datei definieren die `SchoolContext`, `Student`, `Enrollment`, und `Course` Klassen wie folgt:

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      Die `SchoolContext` Klasse leitet sich von `DbContext`, die Verbindung mit der Datenbank verwaltet und Änderungen in den Daten.

      In der `Student` Klasse, beachten Sie, dass Attribute angewendet werden, um die `FirstName`, `LastName`, und `Year` Eigenschaften. Dieses Tutorial verwendet diese Attribute für die datenüberprüfung. Um den Code zu vereinfachen, werden nur diese Eigenschaften mit datenvalidierung Attributen markiert. In einem echten Projekt würde Sie Validierungsattribute, die allen Eigenschaften, die Validierung erforderlich anwenden.

   4. Save UniversityModels.cs.

## <a name="set-up-the-database-based-on-classes"></a>Richten Sie die Datenbank auf Basis von Klassen

Dieses Tutorial verwendet [Code First-Migrationen](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) Objekte erstellen und Datenbanktabellen. Diese Tabellen speichern Informationen über die Schüler/Studenten und ihre Kurse.

   1. Wählen Sie **Tools** > **NuGet Paket-Manager** > **Paket-Manager Konsole**.

   2. In **-Paket-Manager-Konsole**, führen Sie diesen Befehl:  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      Wenn der Befehl erfolgreich abgeschlossen wurde, Meldung eine darauf hinweist, dass Migrationen aktiviert wurden.

      ![Aktivieren von Migrationen](retrieving-data/_static/image8.png)

      Beachten Sie, dass eine Datei mit dem Namen *Configuration.cs* erstellt wurde. Die `Configuration` -Klasse verfügt über eine `Seed` -Methode, die die Datenbanktabellen mit Testdaten vorab auffüllen kann.

## <a name="pre-populate-the-database"></a>Die Datenbank vorab auffüllen

   1. Öffnen Sie Configuration.cs.
   
   2. Fügen Sie der `Seed` -Methode folgenden Code hinzu. Fügen Sie außerdem eine `using` -Anweisung für die `ContosoUniversityModelBinding. Models` Namespace.

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. Configuration.cs zu speichern.

   4. Führen Sie in der Paket-Manager-Konsole den Befehl **hinzufügen-Migration Initial**.

   5. Führen Sie den Befehl **Update-Database**.

      Wenn ein Fehler, beim Ausführen dieses Befehls auftritt, der `StudentID` und `CourseID` Werte möglicherweise sich von der `Seed` Methode Werte. Öffnen Sie die Datenbanktabellen und suchen Sie die vorhandenen Werte für `StudentID` und `CourseID`. Fügen Sie diese Werte auf den Code für das seeding der `Enrollments` Tabelle.

## <a name="add-a-gridview-control"></a>Hinzufügen einer GridView-Steuerelement

Mit Datenbankdaten aufgefüllten können Sie nun die Daten abzurufen und anzuzeigen. 

1. Öffnen Sie Students.aspx.

2. Suchen Sie die `MainContent` Platzhalter. In dieser Platzhalter Hinzufügen einer **GridView** -Steuerelement, das dieser Code enthält.

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   Beachten Sie Folgendes:
   * Beachten Sie, dass der Wert festgelegt wird, für die `SelectMethod` Eigenschaft im GridView-Element. Dieser Wert gibt die Methode zum Abrufen von GridView-Daten, die Sie im nächsten Schritt erstellen. 
   
   * Die `ItemType` -Eigenschaftensatz auf die `Student` Klasse, die zuvor erstellt haben. Mit dieser Einstellung können Sie Eigenschaften der Klasse im Markup zu verweisen. Z. B. die `Student` -Klasse verfügt über eine Sammlung namens `Enrollments`. Können Sie `Item.Enrollments` dieser Sammlung abrufen und anschließend [LINQ-Syntax](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) zum Abrufen von jedem Teilnehmer der Gutschrift Summe registriert.
   
3. Students.aspx zu speichern.

## <a name="add-code-to-retrieve-data"></a>Fügen Sie Code zum Abrufen von Daten

   Fügen Sie in der Students.aspx Code-Behind-Datei für die angegebene Methode der `SelectMethod` Wert. 
   
   1. Öffnen Sie Students.aspx.cs.
   
   2. Hinzufügen `using` -Anweisungen für die `ContosoUniversityModelBinding. Models` und `System.Data.Entity` Namespaces.

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. Fügen Sie die Methode, die Sie für angegebene `SelectMethod`:

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      Die `Include` -Klausel wird die abfrageleistung verbessert, jedoch nicht erforderlich. Ohne die `Include` -Klausel, die Daten werden abgerufen, mithilfe von [ *Lazy Load*](https://en.wikipedia.org/wiki/Lazy_loading), hierbei ist, senden eine separate Abfrage an die Datenbank jedes Mal bezogene Daten abgerufen werden. Mit der `Include` -Klausel, die Daten werden abgerufen, mithilfe von *Eager Load*, was bedeutet, dass eine einzige Datenbankabfrage Ruft alle verknüpften Daten ab. Verwandte Daten verwendet wird, ist eager Loading weniger effizient, da mehr Daten abgerufen werden. Allerdings bietet in diesem Fall unverzüglichem Laden Sie die beste Leistung daran, dass die verknüpften Daten für jeden Datensatz angezeigt werden.

      Weitere Informationen zu Überlegungen zur Leistung beim Laden von Daten, die verknüpft werden, finden Sie unter den **explizite Laden von verknüpften Daten, mittels Eager Load und Lazy** im Abschnitt der [verwandte Lesen von Daten mit dem Entity Framework in einer ASP.NET-Anwendung MVC-Anwendung](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) Artikel.

      Standardmäßig ist die Daten durch die Werte der Eigenschaft als Schlüssel markiert sortiert. Sie können Hinzufügen einer `OrderBy` -Klausel, um einen der anderen Art Wert angeben. In diesem Beispiel ist die Standardeinstellung `StudentID` Eigenschaft wird für die Sortierung verwendet. In der [sortieren, Paging und Filtern von Daten](sorting-paging-and-filtering-data.md) Artikel, der Benutzer wird aktiviert, um eine Spalte für die Sortierung auswählen.
 
   4. Students.aspx.cs zu speichern.

## <a name="run-your-application"></a>Führen Sie die Anwendung 

Führen Sie Ihre Webanwendung (**F5**) und navigieren Sie zu der **Schüler/Studenten** Seite, das Folgendes anzeigt:

   ![Anzeigen von Daten](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>Automatische Generierung von SMO-Methoden-Bindung

Wenn dieser tutorialreihe durcharbeiten, können Sie den Code einfach aus dem Lernprogramm zu Ihrem Projekt kopieren. Ein Nachteil dieses Ansatzes ist jedoch, dass Sie nicht über die Funktion, die von Visual Studio zum automatischen Generieren von Code für die Bindung SMO-Methoden bereitgestellt werden können. Bei der Arbeit an Ihre eigenen Projekte sparen automatische codegenerierung Sie Zeit und die Hilfe, die Sie wissen, wie Implementieren eines Vorgangs erhalten. Dieser Abschnitt beschreibt die automatischen Code Generation-Funktion. In diesem Abschnitt dient nur zu Informationszwecken und keinen Code, die, den Sie in Ihrem Projekt implementieren müssen. 

Beim Festlegen eines Werts für die `SelectMethod`, `UpdateMethod`, `InsertMethod`, oder `DeleteMethod` Eigenschaften im Markupcode, Sie können auswählen, die **neue Methode erstellen** Option.

![Erstellen Sie eine Methode](retrieving-data/_static/image18.png)

Visual Studio nicht nur eine Methode im Code-Behind mit der richtigen Signatur erstellt, sondern generiert auch den Implementierungscode, um den Vorgang auszuführen. Wenn Sie zuerst festlegen, die `ItemType` Eigenschaft, bevor Sie die automatische codegenerierung mit Features, die generierten Code verwendet, die eingeben, und für die Vorgänge. Beispielsweise wird beim Festlegen der `UpdateMethod` -Eigenschaft, den folgenden Code wird automatisch generiert:

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

Dieser Code muss in diesem Fall nicht zu Ihrem Projekt hinzugefügt werden. Im nächsten Tutorial implementieren Sie Methoden zum Aktualisieren, löschen und neue Daten hinzufügen.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial erstellten Data Model-Klassen und eine Datenbank aus den Klassen generiert. Sie können Tabellen der Datenbank mit Testdaten gefüllt. Sie wurden die modellbindung zum Abrufen von Daten aus der Datenbank verwendet, und klicken Sie dann die Daten in einer GridView-Ansicht angezeigt.

In den nächsten [Tutorial](updating-deleting-and-creating-data.md) in dieser Reihe werde Sie aktivieren, aktualisieren, löschen und Erstellen von Daten.

> [!div class="step-by-step"]
> [Nächste](updating-deleting-and-creating-data.md)
