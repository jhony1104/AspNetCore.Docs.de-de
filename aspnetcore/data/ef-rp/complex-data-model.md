---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)'
author: rick-anderson
description: In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie Regeln zur Formatierung, Validierung und Zuordnung angeben.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 56c2d783e8c75aad7487aa03309bb962f4354567
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346527"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Razor-Seiten mit EF Core in ASP.NET Core: Datenmodell (5 von 8)

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand. In diesem Tutorial wird Folgendes durchgeführt:

* Weitere Entitäten und Beziehungen werden hinzugefügt.
* Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.

Die Entitätsklassen des vollständigen Datenmodells werden in der folgenden Abbildung dargestellt:

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.

## <a name="customize-the-data-model-with-attributes"></a>Anpassen des Datenmodells mithilfe von Attributen

In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.

### <a name="the-datatype-attribute"></a>Das DataType-Attribut

Die Seite für Studenten zeigt derzeit die Uhrzeit des Anmeldedatums an. Üblicherweise zeigen Datumsfelder nur das Datum an, nicht die Uhrzeit.

Aktualisieren Sie *Models/Student.cs* mit folgendem hervorgehobenen Code:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist. In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit. Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen. Beispiel:

* Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.
* Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.

Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus, die von HTML5-Browsern genutzt werden. Die `DataType`-Attribute bieten keine Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird. Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden. Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.

Das `DisplayFormat`-Attribut kann eigenständig verwendet werden. Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden. Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:

* Der Browser kann HTML5-Features aktivieren. Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.
* Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.

Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).

Führen Sie die App aus. Navigieren Sie zur Indexseite „Studenten“. Die Uhrzeiten werden nicht mehr angezeigt. Jede Ansicht, die das `Student`-Modell verwendet, zeigt das Datum ohne Uhrzeit an.

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Das StringLength-Attribut

Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden. Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an. Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit. Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.

Aktualisieren Sie das `Student`-Modell mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Der vorangehende Code beschränkt Namen auf maximal 50 Zeichen. Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt. Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) wird verwendet, um Einschränkungen auf die Eingabe anzuwenden. Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Führen Sie die App aus:

* Navigieren Sie zur Seite für Studenten.
* Klicken Sie auf **Neu erstellen**, und geben Sie einen Namen ein, der länger als 50 Zeichen ist.
* Wenn Sie auf **Erstellen** klicken, zeigt die clientseitige Validierung eine Fehlermeldung an.

![Indexseite „Studenten“, die Fehler für die Zeichenfolgenlänge anzeigt](complex-data-model/_static/string-length-errors.png)

Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt. Die Namensfelder weisen den Typ `nvarchar(MAX)` auf, da die Migrationen nicht auf der Datenbank ausgeführt wurden. Wenn die Migrationen im Verlauf dieses Tutorials ausgeführt werden, ändern sich die Namensfelder in `nvarchar(50)`.

### <a name="the-column-attribute"></a>Das Column-Attribut

Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden. In diesem Abschnitt wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.

Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).

Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.

Aktualisieren Sie die Datei *Student.cs* mit folgendem hervorgehobenen Code:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Durch die zuvor vorgenommene Änderung wird `Student.FirstMidName` in der App der `FirstName`-Spalte der `Student`-Tabelle zugeordnet.

Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt. Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein. Wenn die App ausgeführt wird, bevor Migrationen angewendet werden, wird folgende Ausnahme generiert:

```SQL
SqlException: Invalid column name 'FirstName'.
```

So aktualisieren Sie die Datenbank:

* Erstellen Sie das Projekt.
* Öffnen Sie ein Befehlsfenster im Projektordner. Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

------

Der Befehl `migrations add ColumnFirstName` generiert folgende Warnmeldung:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind. Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.

* Testen Sie die App.

Öffnen Sie die Tabelle „Student“ im SSOX:

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf. Die Namensspalten weisen nun den Typ `nvarchar(50)` auf. Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.

> [!Note]
> Im folgenden Abschnitt werden beim Erstellen der App in einigen Phasen Compilerfehler generiert. Diese Anweisungen präzisieren, wann die App erstellt werden soll.

## <a name="student-entity-update"></a>Aktualisieren der Entität „Student“

![Entität „Student“](complex-data-model/_static/student-entity.png)

Aktualisieren Sie *Models/Student.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Das Attribut „Required“

Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern. Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (`DateTime`, `int`, `double` usw.) nicht erforderlich. Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.

Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Das Display-Attribut

Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll. Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).

### <a name="the-fullname-calculated-property"></a>Die berechnete FullName-Eigenschaft

Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird. `FullName` verfügt lediglich über einen get-Accessor und kann daher nicht festgelegt werden. In der Datenbank wird keine `FullName`-Spalte erstellt.

## <a name="create-the-instructor-entity"></a>Erstellen der Instructor-Entität

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

Erstellen Sie *Models/Instructor.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

In einer Zeile können mehrere Attribute enthalten sein. Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“

Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.

Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Folgendes gilt, wenn eine Navigationseigenschaft mehrere Entitäten enthält:

* Bei der Eigenschaft muss es sich um einen Listentyp handeln, bei dem Einträge hinzugefügt, gelöscht und aktualisiert werden können.

Folgendes gilt für die Typen von Navigationseigenschaften:

* `ICollection<T>`
*  `List<T>`
*  `HashSet<T>`

Wenn `ICollection<T>` angegeben wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.

Die `CourseAssignment`-Entität wird im Abschnitt über m:n-Beziehungen erläutert.

Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann. Die `OfficeAssignment`-Eigenschaft enthält also eine einzige `OfficeAssignment`-Entität. `OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Erstellen der Entität „OfficeAssignment“

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Das Key-Attribut

Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.

Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität. Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist. Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität. Entity Framework Core erkennt `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment`, weil

* `InstructorID` der Namenskonvention „ID“ oder „classnameID“ nicht folgt.

Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:

```csharp
[Key]
public int InstructorID { get; set; }
```

Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.

### <a name="the-instructor-navigation-property"></a>Die Navigationseigenschaft „Instructor“

Die `OfficeAssignment`-Navigationseigenschaft für die `Instructor`-Entität ist auf NULL festlegbar, weil:

* Referenztypen (z.B. Klassen) auf NULL festlegbar sind.
* Einem Dozenten möglicherweise kein Büro zugewiesen ist.


Die `OfficeAssignment`-Entität besitzt eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft, weil:

* `InstructorID` nicht auf NULL festlegbar ist.
* Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.

Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.

Das `[Required]`-Attribut kann auf die `Instructor`-Navigationseigenschaft angewendet werden:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Der vorangehende Code legt fest, dass ein zugehöriger Dozent vorhanden sein muss. Der vorangehende Code ist nicht erforderlich, da der Fremdschlüssel `InstructorID` (der ebenfalls der Primärschlüssel ist) nicht auf NULL festlegbar ist.

## <a name="modify-the-course-entity"></a>Ändern der Entität „Course“

![Entität „Course“](complex-data-model/_static/course-entity.png)

Aktualisieren Sie *Models/Course.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`. `DepartmentID` zeigt auf die verknüpfte `Department`-Entität. Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.

Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.

Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank. Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel. Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden. Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID` *nicht* enthalten ist. Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:

* Die `Department`-Entität ist NULL, wenn diese nicht explizit geladen wird.
* Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.

Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.

### <a name="the-databasegenerated-attribute"></a>Das Attribut „DatabaseGenerated“

Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden. Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise. Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben, z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.

Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden. Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde. Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:

Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.

## <a name="create-the-department-entity"></a>Erstellen der Entität „Department“

![Entität „Department“](complex-data-model/_static/department-entity.png)

Erstellen Sie *Models/Department.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Das Column-Attribut

Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern. Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern. Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich. Entity Framework Core wählt üblicherweise den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus. Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet. Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:

* Ein Fachbereich kann einen Administrator besitzen oder nicht.
* Bei einem Administrator handelt es sich immer um einen Dozenten. Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.

Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.

Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:

```csharp
public ICollection<Course> Courses { get; set; }
```

Hinweis: Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen. Das kaskadierende Delete kann zu Zirkelregeln für kaskadierende Deletes führen. Durch Zirkelregeln für kaskadierende Deletes wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.

Beispielsweise dann, wenn die `Department.InstructorID`-Eigenschaft nicht als auf NULL festlegbar definiert wurde:

* Entity Framework Core konfiguriert eine Regel für kaskadierende Deletes, um den Dozenten zu löschen, wenn ein Fachbereich gelöscht wird.
* Das Löschen eines Dozenten in Folge des Löschens eines Fachbereichs stellt nicht das beabsichtigte Verhalten dar.

Wenn die Geschäftsregeln erfordern, dass die `InstructorID`-Eigenschaft nicht auf NULL festgelegt werden darf, verwenden Sie folgende Fluent-API-Anweisung:

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

Durch den vorangehenden Code werden kaskadierende Deletes für die Beziehung zwischen „Department“ und „Instructor“ deaktiviert.

## <a name="update-the-enrollment-entity"></a>Aktualisieren der Entität „Enrollment“

Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:

Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>n:n-Beziehungen

Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität. Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*. „Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.

Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen. (Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert. Das Erstellen des Diagramms ist nicht Teil des Tutorials.)

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

Jede Beziehung weist an einem Ende „1“ und am anderen Ende „*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.

Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten. Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.

Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.

Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht. Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>Die Entität „CourseAssignment“

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Beziehung zwischen „Instructor“ und „Course“

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

Folgendes gilt für die m:n-Beziehung zwischen „Instructor“ und „Course“:

* Eine Jointabelle ist erforderlich, die durch eine Entitätenmenge dargestellt werden muss.
* Diese muss eine reine Jointabelle (eine Tabelle ohne Nutzlast) sein.

Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen. Der Name der Jointabelle für „Instructor“ und „Course“ lautet mithilfe dieses Musters beispielsweise `CourseInstructor`. Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.

Datenmodelle fangen einfach an und werden dann größer. Währenddessen erhalten Joins ohne Nutzlast häufig Nutzlasten. Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert. Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne. „Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden. Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment` `CourseInstructor` vorzuziehen.

### <a name="composite-key"></a>Zusammengesetzte Schlüssel

Fremdschlüssel sind nicht auf NULL festlegbar. Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig. `CourseAssignment` erfordert keinen dedizierten Fremdschlüssel. Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel. Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden. Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.

Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:

* Mehrere Zeilen für einen Kurs zulässig sind.
* Mehrere Zeilen für einen Dozenten zulässig sind.
* Mehrere Zeilen für denselben Dozenten und Kurs nicht zulässig sind.

Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind. So verhindern Sie solche Duplikate:

* Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder
* konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt. Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Aktualisieren des Datenbankkontexts

Fügen Sie folgenden hervorgehobenen Code zu *Data/SchoolContext.cs* hinzu:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.

## <a name="fluent-api-alternative-to-attributes"></a>Fluent-API-Alternativen für Attribute

Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core. Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird. Der [folgende Code](/ef/core/modeling/#methods-of-configuration) veranschaulicht die Fluent-API beispielhaft:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann. Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.

Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden. Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.

Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten. Attribute und die Fluent-API können gemeinsam verwendet werden. Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels. Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können. Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:

* Wählen Sie einen der beiden Ansätze aus.
* Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.

Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:

* Nur für die Validierung (z.B. `MinimumLength`)
* Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)
* Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)

Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Entitätsdiagramm mit angezeigten Beziehungen

Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

Das vorherige Diagramm stellt Folgendes dar:

* Mehrere Linien für 1:n-Beziehungen (1:\*)
* Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.
* Die Linie für die 0..1:n-Beziehung (0..1:*) zwischen den Entitäten `Instructor` und `Department`.

## <a name="seed-the-db-with-test-data"></a>Ausführen eines Seedings mit Testdaten für die Datenbank

Aktualisieren Sie den Code in *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit. Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen. Die Beispieldaten werden für Tests verwendet. Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.

## <a name="add-a-migration"></a>Hinzufügen einer Migration

Erstellen Sie das Projekt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

------

Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Anwenden der Migration

Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie zukünftig Änderungen an dieser vorgenommen werden sollen. In diesem Tutorial werden zwei Vorgehensweisen veranschaulicht:

* [Löschen und Neuerstellen der Datenbank](#drop)
* [Anwenden der Migration auf die vorhandene Datenbank](#applyexisting). Obwohl diese Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen. **Hinweis:** Dies ist ein optionaler Abschnitt des Tutorials. Sie können diesen Abschnitt überspringen und die Schritte zum Löschen und Neuerstellen durchführen. Wenn Sie stattdessen die Schritte in diesem Abschnitt ausführen möchten, führen Sie nicht die Schritte zum Löschen und Neuerstellen aus. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Löschen und Neuerstellen der Datenbank

Durch den Code in der aktualisierten `DbInitializer`-Klasse werden Startwertdaten für die neuen Entitäten hinzugefügt. Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:

```PMC
Drop-Database
Update-Database
```

Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner. Der Projektordner enthält die Datei *Startup.cs*.

Geben Sie im Befehlsfenster Folgendes ein:

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

------

Führen Sie die App aus. Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt. Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank auf.

Öffnen Sie die Datenbank im SSOX:

* Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.
* Erweitern Sie den Knoten **Tabellen**. Die erstellten Tabellen werden angezeigt.

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

Überprüfen Sie die Tabelle **CourseAssignment**:

* Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment**, und klicken Sie auf **Daten anzeigen**.
* Überprüfen Sie, ob die Tabelle **CourseAssignment**-Daten enthält.

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Anwenden der Migration auf die vorhandene Datenbank

Dieser Abschnitt ist optional. Diese Schritte funktionieren nur, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.

Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden. Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren. In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten. Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen. Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert haben.

Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu. Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.

So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:

* Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.
* Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.

#### <a name="fix-the-foreign-key-constraints"></a>Aufheben der Fremdschlüsseleinschränkungen

Aktualisieren Sie die `Up`-Methode der `ComplexDataModel`-Klasse:

* Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.
* Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Fügen Sie folgenden hervorgehobenen Code hinzu: Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel` `Up` ausgeführt wurde.

Ein Produktions-App würde:

* Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen
* Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden

Im folgenden Tutorial werden verknüpfte Daten behandelt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube (Teil 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Dieses Tutorial auf YouTube (Teil 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

::: moniker-end

> [!div class="step-by-step"]
> [Zurück](xref:data/ef-rp/migrations)
> [Weiter](xref:data/ef-rp/read-related-data)
