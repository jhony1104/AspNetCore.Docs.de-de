---
title: 'Tutorial: Erste Schritte mit EF Core in einer ASP.NET Core MVC-Web-App'
description: Dies ist die erste Tutorial in der Reihe, in dem die Erstellung der Beispielanwendung der Contoso University von Grund auf erläutert wird.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511300"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Tutorial: Erste Schritte mit EF Core in einer ASP.NET Core MVC-Web-App

Dieses Tutorial wurde **nicht** auf ASP.NET Core 3.0 aktualisiert. Die [Razor Pages-Version](xref:data/ef-rp/intro) wurde aktualisiert. Die meisten Codeänderungen für ASP.NET Core 3.0 und höher in diesem Tutorial:

* befinden sich in den Dateien *Startup.cs* und *Program.cs*
* finden Sie in der [Razor Pages-Version](xref:data/ef-rp/intro) 

Informationen zum Zeitpunkt des Updates finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/13920).

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Die Beispielwebanwendung der Contoso University veranschaulicht, wie mit Entity Framework Core 2.2 (EF Core 2.2) und Visual Studio 2017 oder 2019 ASP.NET Core 2.2 MVC-Webanwendungen erstellt werden.

Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University. Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten. Dies ist die erste Tutorial in der Reihe, in dem die Erstellung der Beispielanwendung der Contoso University von Grund auf erläutert wird.

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen einer ASP.NET Core-MVC-Web-App
> * Einrichten des Websitestils
> * Erfahren Sie mehr über EF Core-NuGet-Pakete
> * Erstellen des Datenmodells
> * Erstellen des Datenbankkontexts
> * Registrieren des Kontexts für die Abhängigkeitsinjektion
> * Initialisieren der Datenbank mithilfe von Testdaten
> * Erstellen von einem Controller und Ansichten
> * Zeigen Sie die Datenbank an

## <a name="prerequisites"></a>Voraussetzungen

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit den folgenden Workloads:
  * Workload **ASP.NET und Webentwicklung**
  * Workload **Plattformübergreifende .NET Core-Entwicklung**

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) zu vergleichen. Eine Liste mit häufig auftretenden Fehlern und den jeweiligen Lösungen finden Sie im Abschnitt [zur Fehlerbehebung auf im letzten Tutorial dieser Tutorialreihe](advanced.md#common-errors). Wenn Sie dort nicht die gewünschten Informationen finden, können Sie unter „StackOverflow.com“ für [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) eine Frage posten.

> [!TIP]
> Diese Reihe besteht aus 10 Tutorials, die aufeinander aufbauen. Sie sollten jedes Mal, wenn Sie erfolgreich ein Tutorial abgeschlossen haben, eine Kopie des Projekts erstellen. Wenn Sie dann auf Probleme stoßen, können Sie zurück zum vorherigen Tutorial wechseln und müssen nicht wieder ganz von vorne beginnen.

## <a name="contoso-university-web-app"></a>Web-App der Contoso University

Bei der Anwendung, die Sie mithilfe dieser Tutorials erstellen, handelt es sich um eine einfache Universitätswebsite.

Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen. Nachfolgend werden einige Anzeigen dargestellt, die erstellt werden sollen.

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Erstellen einer Web-App

* Öffnen Sie Visual Studio.

* Klicken Sie im Menü **Datei** auf **Neu > Projekt**.

* Klicken Sie im linken Bereich auf **Installiert > Visual C# > Web**.

* Wählen Sie die Projektvorlage **ASP.NET Core-Webanwendung** aus.

* Geben Sie **ContosoUniversity** als Name ein, und klicken Sie auf **OK**.

  ![Dialogfeld "Neues Projekt"](intro/_static/new-project2.png)

* Warten Sie, bis das Dialogfeld **Neue ASP.NET Core-Webanwendung** angezeigt wird.

* Wählen Sie **.NET Core**, **ASP.NET Core 2.2** und die Vorlage **Webanwendung (Model-View-Controller)** aus.

* Stellen Sie sicher, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.

* Wählen Sie **OK** aus.

  ![Dialogfeld „Neues ASP.NET Core-Projekt“](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.

Öffnen Sie *Views/Shared/_Layout.cshtml*, und nehmen Sie die folgenden Änderungen vor:

* Ändern Sie jedes „ContosoUniversity“ in „Contoso University“. Diese Begriffskombination kommt dreimal vor.

* Fügen Sie Menüeinträge für **Informationen**, **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Datenschutz**.

Die Änderungen werden hervorgehoben.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

Ersetzen Sie in der *Views/Home/Index.cshtml*-Datei die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser Anwendung:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Drücken Sie STRG+F5, um das Projekt auszuführen, oder wählen Sie aus dem Menü **Debuggen > Ohne Debuggen starten** aus. Dann wird Ihnen die Startseite mit Registerkarten für die Seiten angezeigt, die Sie mithilfe dieses Tutorials erstellen.

![Contoso University-Startseite](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Informationen zu EF Core-NuGet-Paketen

Installieren Sie den Datenbankanbieter, der verwendet werden soll, um einem Projekt EF Core-Unterstützung hinzuzufügen. In diesem Tutorial wird SQL Server verwendet, und das Anbieterpaket lautet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Dieses Paket ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten, weshalb Sie nicht auf das Paket verweisen müssen.

Das EF SQL Server-Paket und dessen Abhängigkeiten (`Microsoft.EntityFrameworkCore` und `Microsoft.EntityFrameworkCore.Relational`) stellen für EF Runtimeunterstützung bereit. Sie fügen später im Laufe des [Migrations](migrations.md)-Tutorials ein Paket mit Tools hinzu.

Informationen zu anderen Datenbankanbietern, die für Entity Framework Core verfügbar sind, finden Sie unter [Datenbankanbieter](/ef/core/providers/).

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Als nächstes erstellen Sie Entitätsklassen für die Contoso University-Anwendung. Beginnen Sie mit dem folgenden drei Entitäten.

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

Es besteht eine 1:n-Beziehung zwischen den Entitäten `Student` und `Enrollment`. Außerdem besteht eine 1:n-Beziehung zwischen den Entitäten `Course` und `Enrollment`. Das bedeutet, dass ein Student für beliebig viele Kurse angemeldet sein kann und sich für jeden Kurs eine beliebige Anzahl von Studenten anmelden kann.

In den folgenden Abschnitten erstellen Sie für jede dieser Entitäten eine Klasse.

### <a name="the-student-entity"></a>Die Entität „Student“

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

Erstellen Sie im Ordner *Models* (Modelle) die Klassendatei *Student.cs*, und ersetzen Sie den Vorlagencode durch folgenden Code.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht. Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.

Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships). Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind. In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit der `Student`-Entität in Zusammenhang stehen. Das heißt: Wenn eine vorhandene „Student“-Zeile in der Datenbank über zwei zugehörige „Enrollment“-Zeilen (Zeilen, in denen der Primärschlüsselwert dieses Studenten in der StudentID-Fremdschlüsselspalte enthalten ist), enthält die `Enrollments`-Navigationseigenschaft dieser `Student`-Entität diese beiden `Enrollment`-Entitäten.

Wenn eine Navigationseigenschaft mehrere Entitäten enthalten kann (wie bei m:n- oder 1:n-Beziehungen), muss dessen Typ aus einer Liste bestehen, in der Einträge hinzugefügt, gelöscht und aktualisiert werden können – z.B.: `ICollection<T>`. Sie können die `ICollection<T>`-Instanz oder einen Typ wie `List<T>` oder `HashSet<T>` angeben. Wenn Sie `ICollection<T>` angeben, erstellt EF standardmäßig eine `HashSet<T>`-Auflistung.

### <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

Erstellen Sie im Ordner *Models* (Modelle) die Datei *Enrollment.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Die `EnrollmentID`-Eigenschaft wird als Primärschlüssel verwendet. Diese Entität verwendet das `classnameID`-Muster anstelle der `ID` alleine, wie in der `Student`-Entität dargestellt wurde. Normalerweise würden Sie nur ein Muster auswählen und dieses für das gesamte Datenmodell verwenden. Diese Variation soll verdeutlichen, dass Sie ein beliebiges Muster erstellen können. In einem der [nächsten Tutorials](inheritance.md) wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.

Die `Grade`-Eigenschaft ist `enum`. Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt. Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft. Eine `Enrollment`-Entität wird einer `Student`-Entität zugeordnet, damit die Eigenschaft nur eine `Student`-Entität enthalten kann. Dies steht im Gegensatz zu der bereits erläuterten `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthalten kann.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und die zugehörige Navigationseigenschaft lautet `Course`. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entity Framework interpretiert Eigenschaften als Fremdschlüsseleigenschaften, wenn Sie den Namen `<navigation property name><primary key property name>` haben – z.B. `StudentID` für die `Student`-Navigationseigenschaft, da der Primärschlüssel der `Student`-Entität `ID` lautet. Fremdschlüsseleigenschaften können auch einfach den Namen `<primary key property name>` haben – z.B. `CourseID`, da der Primärschlüssel der `Course`-Entität `CourseID` lautet.

### <a name="the-course-entity"></a>Die Entität „Course“

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

Erstellen Sie im Ordner *Models* (Modelle) die Datei *Course.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Weitere Informationen zum `DatabaseGenerated`-Attribut erhalten Sie in einem [späteren Tutorial](complex-data-model.md) dieser Reihe. Im Grunde können Sie über dieses Attribut den Primärschlüssel für den Kurs angeben, anstatt ihn von der Datenbank generieren zu lassen.

## <a name="create-the-database-context"></a>Erstellen des Datenbankkontexts

Die Datenbankkontextklasse ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert. Sie können diese Klasse durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellen. Sie geben in Ihrem Code an, welche Entitäten im Datenmodell enthalten sind. Außerdem können Sie bestimmte Entity Framework-Verhalten anpassen. In diesem Projekt heißt die Klasse `SchoolContext`.

Erstellen Sie im Projektordner einen Ordner mit dem Namen *Data* (Daten).

Erstellen Sie im Ordner *Data* (Daten) die Klassendatei *SchoolContext.cs*, und ersetzen Sie den Vorlagencode durch folgenden Code:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Dieser Code erstellt eine `DbSet`-Eigenschaft für jede Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.

Auch wenn Sie die Anweisungen `DbSet<Enrollment>` und `DbSet<Course>` auslassen, ändert dies nichts an der Funktionsweise. Diese sind implizit in Entity Framework enthalten, da die `Student`-Entität auf die `Enrollment`-Entität und die `Enrollment`-Entität auf die `Course`-Entität verweist.

Wenn die Datenbank erstellt wird, erstellt EF Core Tabellen mit Namen, die den `DbSet`-Eigenschaftennamen entsprechen. Eigenschaftennamen für Auflistungen stehen in der Regel im Plural (Students anstelle von Student). Allerdings sind sich Entwickler uneinig darüber, ob auch Tabellennamen im Plural stehen sollten. In diesen Tutorials wird das Standardverhalten außer Kraft gesetzt, indem im DbContext Tabellennamen im Singular angegeben werden. Fügen Sie dafür den hervorgehobenen Code nach der DbSet-Eigenschaft ein.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Registrieren Sie den SchoolContext

ASP.NET Core implementiert standardmäßig [Dependency Injection](../../fundamentals/dependency-injection.md). Dienste wie der EF-Datenbankkontext werden per Dependency Injection beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z.B. Ihre MVC-Controller), werden über Konstruktorparameter bereitgestellt. Nachfolgend in diesem Tutorial wird der Konstruktorcode des Controllers angezeigt, der eine Kontextinstanz abruft.

Öffnen Sie die *Startup.cs*-Datei, und fügen Sie der `ConfigureServices`-Methode die hervorgehobenen Zeilen hinzu, um `SchoolContext` als Dienst zu registrieren.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem `DbContextOptionsBuilder`-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.

Fügen Sie `using`-Anweisungen für die Namespaces `ContosoUniversity.Data` und `Microsoft.EntityFrameworkCore` hinzu, und erstellen Sie dann das Projekt.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Öffnen Sie die *appsettings.json*-Datei, und fügen Sie wie im folgenden Beispiel dargestellt eine Verbindungszeichenfolge hinzu.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Die Verbindungszeichenfolge gibt eine SQL Server-LocalDB-Datenbank an. LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar zur Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.

## <a name="initialize-db-with-test-data"></a>Initialisieren Sie die Datenbank mit Testdaten

Entity Framework erstellt eine leere Datenbank für Sie. In diesem Abschnitt schreiben Sie eine Methode, die aufgerufen wird, nachdem die Datenbank erstellt wurde, um diese mit Testdaten aufzufüllen.

Verwenden Sie an dieser Stelle die `EnsureCreated`-Methode, um die Datenbank automatisch zu erstellen. In einem [späteren Tutorial](migrations.md) wird dargestellt, wie Sie mit Änderungen an dem Modell umgehen können, indem Sie Code First-Migrationen verwenden, um das Datenbankschema zu verwenden, anstatt die Datenbank zu verwerfen und neu zu erstellen.

Erstellen Sie im Ordner *Data* (Daten) eine neue Klassendatei mit dem Namen *DbInitializer.cs*, und ersetzen Sie den Vorlagencode durch den folgenden Code, wodurch, falls nötig, eine Datenbank erstellt wird und Testdaten in eine neue Datenbank geladen werden.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Der Code überprüft, ob Studenten in der Datenbank enthalten sind. Wenn dies nicht der Fall ist, nimmt diese an, dass die Datenbank neu ist und mit Testdaten aufgefüllt werden muss. Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.

Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um beim Anwendungsstart die folgenden Vorgänge auszuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.
* Löschen Sie den Kontext, nachdem die Seedmethode abgeschlossen wurde.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Fügen Sie `using`-Anweisungen hinzu:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Es kann sein, dass in älteren Tutorials ähnlicher Code in der `Configure`-Methode in der *Startup.cs*-Datei angezeigt wird. Es wird empfohlen, dass Sie die `Configure`-Methode nur verwenden, um die Anforderungspipeline einzurichten. Der Code für den Anwendungsstart gehört zur `Main`-Methode.

Wenn dann die Anwendung das erste Mal ausgeführt wird, wird die Datenbank erstellt und mit Testdaten aufgefüllt. Wenn Sie Ihr Datenmodell ändern, können Sie die Datenbank löschen, die Seedmethode aktualisieren, und mit einer neuen Datenbank auf die gleiche Weise beginnen. In späteren Tutorials wird ein Update für die Datenbank ausgeführt, wenn das Datenmodell geändert wird, ohne die Datenbank zu löschen und neu zu erstellen.

## <a name="create-controller-and-views"></a>Erstellen Sie Controller und Ansichten

Verwenden Sie als nächstes die Gerüstbauengine in Visual Studio, um einen MVC-Controller und Ansichten hinzuzufügen, die EF verwenden, um Daten abzufragen und zu speichern.

Die automatische Erstellung von CRUD-Aktionsmethoden und Ansichten wird als Gerüstbau bezeichnet. Gerüstbau und Codegeneration unterscheiden sich insofern als der Gerüstbaucode ein Startpunkt ist, den Sie Ihren eigenen Anforderungen entsprechend verändern können. Generierter Code wird in der Regel nicht verändert. Wenn Sie generierten Code anpassen müssen, verwenden Sie partielle Klassen, oder generieren Sie den Code erneut, wenn Änderungen vorgenommen werden.

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie auf **Hinzufügen > Neues Gerüstelement**.

* Im Dialogfeld **Gerüst hinzufügen**:

  * Wählen Sie **MVC-Controller mit Ansichten unter Verwendung von Entity Framework** aus.

  * Klicken Sie auf **Hinzufügen**. Das Dialogfeld **MVC-Controller mit Ansichten unter Verwendung von Entity Framework hinzufügen** wird angezeigt.

    ![Gerüst „Student“](intro/_static/scaffold-student2.png)

  * Wählen Sie unter **Modellklasse** **Student** aus.

  * Wählen Sie in der **Datenkontextklasse** **SchoolContext** aus.

  * Akzeptieren Sie den Standardnamen **StudentsController**.

  * Klicken Sie auf **Hinzufügen**.

  Wenn Sie auf **Hinzufügen** klicken, erstellt die Visual Studio-Gerüstbauengine eine *StudentsController.cs*-Datei und mehrere Ansichten ( *.cshtml*-Dateien), die mit dem Controller zusammenarbeiten.

(Die Gerüstbauengine kann ebenfalls für Sie den Datenbankkontext erstellen, wenn Sie diesen nicht zuvor manuell erstellt haben, wie obenstehend in diesem Tutorial beschrieben. Sie können im Feld **Controller hinzufügen** eine neue Kontextklasse hinzufügen, indem Sie rechts neben der **Datenkontextklasse** auf das Pluszeichen klicken.  Visual Studio erstellt dann neben dem Controller und den Ansichten Ihre `DbContext`-Klasse.)

Der Controller verwendet einen `SchoolContext`-Kontext als Konstruktorparameter.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

Über die Abhängigkeitsinjektion in ASP.NET Core wird eine Instanz von `SchoolContext` an den Controller übergeben. Diese Konfiguration haben Sie bereits in der Datei *Startup.cs*vorgenommen.

Der Controller enthält eine `Index`-Aktionsmethode, über die alle Studenten in der Datenbank angezeigt werden. Die Methode ruft eine Listen von Studenten aus der Entitätenmenge „Student“ ab, indem sie die `Students`-Eigenschaft aus der Datenbankkontextinstanz liest:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Später in diesem Tutorial erfahren Sie mehr zu den Elementen für die asynchrone Programmierung in diesem Code.

In der *Views/Students/Index.cshtml*-Ansicht wird diese Liste in einer Tabelle dargestellt:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Drücken Sie STRG+F5, um das Projekt auszuführen, oder wählen Sie aus dem Menü **Debuggen > Ohne Debuggen starten** aus.

Klicken Sie auf die Registerkarte „Students“, um die Testdaten abzurufen, die über die `DbInitializer.Initialize`-Methode eingefügt wurden. Je nachdem, wie klein Ihr Browserfenster dargestellt wird, sehen Sie einen `Students`-Registerkartenlink unten auf der Seite, oder Sie müssen auf das Navigationssymbol in der oberen rechten Ecke klicken, damit der Link angezeigt wird.

![Contoso University-Startseite verkleinert](intro/_static/home-page-narrow.png)

![Indexseite „Studenten“](intro/_static/students-index.png)

## <a name="view-the-database"></a>Zeigen Sie die Datenbank an

Nachdem Sie die Anwendung gestartet haben, ruft die `DbInitializer.Initialize`-Methode `EnsureCreated` auf. EF hat festgestellt, dass noch keine Datenbank vorhanden war und hat deshalb eine erstellt. Anschließend hat der restliche `Initialize`-Methodencode die Datenbank mit Daten aufgefüllt. Sie können den **SQL Server-Objekt-Explorer** (SSOX) verwenden, um die Datenbank in Visual Studio abzurufen.

Schließen Sie den Browser.

Wenn das SSOX-Fenster noch nicht geöffnet ist, wählen Sie es aus dem Menü **Ansicht** in Visual Studio aus.

Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Datenbanken**, und klicken Sie anschließend auf den Eintrag zu Ihrem Datenbanknamen, der sich in der Verbindungszeichenfolge in Ihrer *appsettings.json*-Datei befindet.

Erweitern Sie den Knoten **Tabellen**, um die Tabellen in Ihrer Datenbank anzuzeigen.

![Tabellen im SSOX](intro/_static/ssox-tables.png)

Klicken Sie mit der rechten Maustaste auf die Tabelle **Students**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.

![Tabelle „Student“ im SSOX](intro/_static/ssox-student-table.png)

Die Datenbankdateien im Format *MDF* und *LDF* befinden sich im Ordner *C:\Benutzer\\\<Ihr Benutzername>* .

Da Sie `EnsureCreated` in der Initialisierermethode aufrufen, die beim App-Start ausgeführt wird, können Sie Änderungen an der `Student`-Klasse vornehmen, die Datenbank löschen oder die Anwendung erneut ausführen. Dann wird Ihre Datenbank automatisch Ihren Änderungen entsprechend neu erstellt. Wenn Sie z.B. eine `EmailAddress`-Eigenschaft zu der `Student`-Klasse hinzufügen, wird eine neue `EmailAddress`-Spalte in der neu erstellten Tabelle angezeigt.

## <a name="conventions"></a>Konventionen

Sie mussten nur wenig Code schreiben, damit Entity Framework eine vollständige Datenbank erstellen kann, da Konventionen oder Annahmen von Entity Framework verwendet werden.

* Die Namen der `DbSet`-Eigenschaften werden als Tabellennamen verwendet. Für Entitäten, auf die nicht über eine `DbSet`-Eigenschaft verwiesen wird, werden Entitätsklassennamen als Tabellennamen verwendet.

* Eigenschaftennamen von Entitäten werden als Spaltennamen verwendet.

* Entitätseigenschaften mit dem Namen „ID“ oder „classnameID“ werden als Primärschlüsseleigenschaften erkannt.

* Eigenschaften werden als Fremdschlüsseleigenschaften interpretiert, wenn Sie den Namen *\<Name der Navigationseigenschaft>\<Eigenschaftenname des Primärschlüssels* haben – z. B. `StudentID` für die `Student`-Navigationseigenschaft, da der Primärschlüssel der `Student`-Entität `ID` lautet. Fremdschlüsseleigenschaften können auch einfach den Namen *\<Eigenschaftenname des Primärschlüssels* haben – z. B. `EnrollmentID`, da der Primärschlüssel der `Enrollment`-Entität `EnrollmentID` lautet.

Konventionelles Verhalten kann überschrieben werden. Beispielsweise können Sie, wie bereits in diesem Tutorial erläutert, Tabellennamen explizit angeben. Außerdem können Sie Spaltennamen und jede beliebige Eigenschaft als Primär- oder Fremdschlüssel festlegen. Dies wird in einem [späteren Tutorial](complex-data-model.md) in dieser Reihe erläutert.

## <a name="asynchronous-code"></a>Asynchroner Code

Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.

Durch die Verwendung von asynchronem Code entsteht ein geringes Maß an Mehraufwand zur Laufzeit. In Situationen, in denen nur wenig Datenverkehr verarbeitet werden muss, haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.

Im folgenden Code haben das `async`-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode zur Folge, dass der Code auf asynchrone Weise ausgeführt wird.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Das `async`-Schlüsselwort teilt dem Compiler mit, dass er für einzelne Teile des Methodentexts Rückrufe generieren und das zurückgegebene `Task<IActionResult>`-Objekt automatisch erstellen soll.

* Der Rückgabetyp `Task<IActionResult>` stellt derzeit ausgeführte Arbeiten mit dem Ergebnis von Typ `IActionResult` dar.

* Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt. Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird. Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.

* Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.

Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:

* Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen. Das schließt beispielsweise `ToListAsync`, `SingleOrDefaultAsync` und `SaveChangesAsync` ein. Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind beispielsweise davon ausgeschlossen.

* Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen. Wenn Sie eine beliebige EF-Methode aufrufen, sollten Sie immer das `await`-Schlüsselwort verwenden.

* Wenn Sie von den Leistungsvorteilen des asynchronen Codes profitieren möchten, vergewissern Sie sich, dass auch alle Bibliothekspakete, die Sie verwenden (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie eine ASP.NET Core-MVC-Web-App erstellt
> * Einrichten des Websitestils
> * Haben Sie mehr über EF Core-NuGet-Pakete erfahren
> * Haben Sie das Datenmodell erstellt
> * Haben Sie den Datenbankkontext erstellt
> * Haben Sie den SchoolContext registriert
> * Haben Sie die Datenbank mit Testdaten initialisiert
> * Haben Sie Controller und Ansichten erstellt
> * Haben Sie die Datenbank angezeigt

Im nächsten Tutorial erfahren Sie, wie Sie grundlegende CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen) ausführen.

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie grundlegende CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen) ausführen.

> [!div class="nextstepaction"]
> [Implementieren von grundlegenden CRUD-Funktionen](crud.md)

