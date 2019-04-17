---
title: 'Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8'
author: rick-anderson
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: aff62d760cf0899983cf841f6715f2658b113f82
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012681"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Die Beispiel-Web-App der Contoso University veranschaulicht, wie mit Entity Framework Core (EF Core) eine ASP.NET Core-App mit Razor Pages erstellt werden kann.

Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University. Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten. Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.

[Herunterladen oder Anzeigen der vollständigen App.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Erforderliche Komponenten

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# [<a name="net-core-cli"></a>.NET Core-CLI](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Kenntnisse über [Razor Pages](xref:razor-pages/index). Anfänger sollten den Artikel [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit diesem Tutorial beginnen.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen. Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.

## <a name="the-contoso-university-web-app"></a>Die Web-App der Contoso University

Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.

Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen. Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten. In diesem Tutorial wird Entity Framework Core mit Razor Pages thematisiert. Die Benutzeroberfläche wird nicht erläutert.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Erstellen der Razor Pages-Web-App „ContosoUniversity“

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

* Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Geben Sie dem Projekt den Namen **ContosoUniversity**. Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.
* Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.

Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Führen Sie die App aus.

# [<a name="net-core-cli"></a>.NET Core-CLI](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten. Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:

* Ändern Sie jedes „ContosoUniversity“ in „Contoso University“. Diese Begriffskombination kommt dreimal vor.

* Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.

Die Änderungen werden hervorgehoben. (Es wird *nicht* das gesamte Markup angezeigt.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Erstellen Sie Entitätsklassen für die Contoso University-App. Beginnen Sie mit dem folgenden drei Entitäten:

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität. Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität. Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden. Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.

In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.

### <a name="the-student-entity"></a>Die Entität „Student“

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

Erstellen Sie einen Ordner *Models* (Modelle). Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht. Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel. In `classnameID` ist `classname` der Name der Klasse. Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.

Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships). Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind. In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen. Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten. Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält. Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt. Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1. `StudentID` ist ein Fremdschlüssel in der Tabelle `Enrollment`, der den Studenten in der Tabelle `Student` angibt.

Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln. `ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` kann angegeben werden. Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung. Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.

### <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel. Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`. In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell. In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.

Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`. Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt. Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel und `Student` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält. Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben. Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist. Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben. Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.

### <a name="the-course-entity"></a>Die Entität „Course“

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.

## <a name="scaffold-the-student-model"></a>Erstellen des Gerüsts für das Studentenmodell

In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.

* Erstellen Sie das Projekt.
* Erstellen Sie den Ordner *Pages/Students*.

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.
* Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **HINZUFÜGEN** aus.

Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:

* Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.
* Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen (**+**) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.
* Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.
* Wählen Sie **Hinzufügen** aus.

![CRUD-Dialogfeld](intro/_static/s1.png)

Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).

# [<a name="net-core-cli"></a>.NET Core-CLI](#tab/netcore-cli)

Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Der Gerüstprozess hat folgende Dateien erstellt und geändert:

### <a name="files-created"></a>Erstellte Dateien

* *Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Dateiupdates

* *Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.
* *appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.

Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.

Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.

## <a name="update-main"></a>Aktualisieren der Main-Methode

Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.
* Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.

Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist. Wenn sie vorhanden ist, werden keine Aktionen durchgeführt. Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt. `EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen. Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.

`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:

* Löschen Sie die Datenbank.
* Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.
* Führen Sie die App aus.
* Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.

`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird. Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.

### <a name="test-the-app"></a>Testen der App

Führen Sie die App aus, und akzeptieren Sie die Cookierichtlinie. Diese App bewahrt keine personenbezogenen Informationen auf. Weitere Informationen zur Cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).

* Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.
* Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).

## <a name="examine-the-schoolcontext-db-context"></a>Untersuchen des Datenbankkontexts „SchoolContext“

Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert. Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind. In diesem Projekt heißt die Klasse `SchoolContext`.

Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge. Das heißt für Entity Framework Core:

* Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.
* Entitäten entsprechen Zeilen in Tabellen.

`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden. Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist. Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an. LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten

Entity Framework Core erstellt eine leere Datenbank. In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.

Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`. `Models` entspricht dem vom Gerüst generierten Code. Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).

Der Code überprüft, ob Studenten in der Datenbank enthalten sind. Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert. Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.

Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext. Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.

Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

Löschen Sie alle Datensätze für Studenten, und starten Sie die App neu. Wenn die Datenbank nicht initialisiert wird, legen Sie einen Breakpoint in `Initialize` fest, um das Problem zu diagnostizieren.

## <a name="view-the-db"></a>Abrufen der Datenbank

Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert. Daher lautet der Name der Datenbank „SchoolContext-{GUID}“. Die GUID ist für jeden Benutzer unterschiedlich.
Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).
Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.

Erweitern Sie den Knoten **Tabellen**.

Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.

## <a name="asynchronous-code"></a>Asynchroner Code

Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.

Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht. Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.

Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:
  * Er soll Rückrufe für Teile des Methodentexts generieren.
  * Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird. Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.
* Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt. Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird. Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.
* Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.

Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:

* Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen. Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`. Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.
* Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.
* Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).

Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Weiter](xref:data/ef-rp/crud)
