---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Parallelität (8 von 8)'
author: rick-anderson
description: In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2018
uid: data/ef-rp/concurrency
ms.openlocfilehash: 3fb8ebe415d0619d33302a08e97da78db0ad1d1e
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265510"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Razor-Seiten mit EF Core in ASP.NET Core: Parallelität (8 von 8)

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) und [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren. Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an. [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="concurrency-conflicts"></a>Nebenläufigkeitskonflikte

Ein Nebenläufigkeitskonflikt tritt auf, wenn:

* Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.
* Ein anderer Benutzer dieselbe Entität aktualisiert, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.

Wenn die Parallelitätserkennung nicht aktiviert ist, während gleichzeitige Updates ausgeführt werden, geschieht Folgendes:

* Das letzte Update gilt. Das bedeutet, dass die neuesten zu aktualisierenden Werte in der Datenbank gespeichert werden.
* Das erste der aktuellen Updates geht verloren.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten. Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn im Browser die Indexseite angezeigt wird.

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt. Was daraufhin geschieht ist abhängig davon, wie Sie Nebenläufigkeitskonflikte handhaben.

Die optimistische Nebenläufigkeit umfasst die folgenden Optionen:

* Sie können Nachverfolgen, welche Eigenschaft ein Benutzer geändert hat, und nur die entsprechenden Spalten in der Datenbank aktualisieren.

  In diesem Szenario sollten keine Daten verloren gehen. Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert. Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2. Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können. Dieser Ansatz:
 
  * Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.
  * Ist in einer Web-App in der Regel nicht praktisch. Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen. Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.
  * Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.

* Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.

  Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt. Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt. (Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.

* Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden. In der Regel würde die App:

  * Eine Fehlermeldung anzeigen
  * Den aktuellen Datenstatus anzeigen
  * Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden

  Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt. (Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt). Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.

## <a name="handling-concurrency"></a>Behandlung von Parallelität 

Wenn eine Eigenschaft als ein [Parallelitätstoken](/ef/core/modeling/concurrency) konfiguriert ist:

* Stellt EF Core sicher, dass die Eigenschaft nicht geändert wurde, nachdem sie abgerufen wurde. Die Überprüfung findet statt, wenn [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) oder [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.
* Wenn die Eigenschaft geändert wurde, nachdem sie abgerufen wurde, wird eine [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) ausgelöst. 

Das Datenbank- und Datenmodell müssen konfiguriert sein, um das Auslösen von `DbUpdateConcurrencyException` zu unterstützen.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Erkennen von Nebenläufigkeitskonflikten mit Eigenschaften

Nebenläufigkeitskonflikte können auf der Eigenschaftenebene über das [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0)-Attribut erkannt werden. Das Attribut kann auf mehrere Eigenschaften für das Modell angewendet werden. Weitere Informationen finden Sie unter [Datenanmerkungen-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Das Attribut `[ConcurrencyCheck]` wird in diesem Tutorial nicht verwendet.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Erkennen von Nebenläufigkeitskonflikten mit einer Zeile

Um Nebenläufigkeitskonflikte zu erkennen, wird dem Modell eine [Rowversion](/sql/t-sql/data-types/rowversion-transact-sql)-Nachverfolgungsspalte (Zeilenversion) hinzugefügt.  `rowversion` :

* Ist SQL Server-spezifisch. Andere Datenbanken enthalten möglicherweise keine ähnlichen Features.
* Wird verwendet, um zu bestimmen, dass eine Entität, seit dem Abruf aus der Datenbank, nicht geändert wurde. 

Die Datenbank generiert eine sequenzielle Anzahl von `rowversion`, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird. In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert von `rowversion`. Wenn sich die aktualisierte Zeile geändert hat, geschieht Folgendes:

* `rowversion` entspricht nicht dem abgerufenen Wert.
* Die Befehle `Update` oder `Delete` finden keine Zeile, da die `Where`-Klausel die abgerufene `rowversion` enthält.
* Es wird eine `DbUpdateConcurrencyException` ausgelöst.

In EF Core wird eine Parallelitätsausnahme ausgelöst, wenn keine Zeilen durch einen `Update`- oder `Delete`-Befehl aktualisiert werden.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Hinzufügen einer Nachverfolgungseigenschaft zur Entität „Department“

Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Das [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute)-Attribut gibt an, dass diese Spalte in der `Where`-Klausel der Befehle `Update` und `Delete` enthalten ist. Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor dieser durch SQL-`rowversion` ersetzt wurde.

Die Fluent-API kann auch die Nachverfolgungseigenschaft angeben:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an. Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.

Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück. Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.

Sie können das von EF Core generierte T-SQL im Ausgabefenster von Visual Studio sehen.

### <a name="update-the-db"></a>Aktualisieren der Datenbank

Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.

Erstellen Sie das Projekt. Geben Sie Folgendes in ein Befehlsfenster ein:

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs* wird hinzugefügt.
* Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt. Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* Migrationen werden durchgeführt, um die Datenbank zu aktualisieren.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Erstellen des Gerüsts für das Abteilungsmodell

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Department` für die Modellklasse.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

 Führen Sie den folgenden Befehl aus:

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

------

Der vorherige Befehl erstellt ein Gerüst für das `Department`-Modell. Öffnen Sie das Projekt in Visual Studio.

Erstellen Sie das Projekt.

### <a name="update-the-departments-index-page"></a>Aktualisieren der Indexseite für Abteilungen

Die Gerüstbauengine hat eine `RowVersion`-Spalte auf der Indexseite erstellt, jedoch sollte dieses Feld nicht angezeigt werden. In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis über die Parallelität erlangen. Das letzte Byte ist nicht zwingend eindeutig. Eine echte App würde `RowVersion` oder das letzte Byte von `RowVersion` nicht anzeigen.

Aktualisieren der Indexseite:

* Ersetzen Sie „Index“ durch „Abteilungen“.
* Ersetzen Sie das Markup mit `RowVersion` durch das letzten Byte von `RowVersion`.
* Ersetzen Sie „FirstMidName“durch „FullName“.

Das folgende Markup zeigt die aktualisierte Seite an:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualisieren des Seitenbearbeitungsmodells

Aktualisieren Sie die *pages\departments\edit.cshtml.cs*-Datei mithilfe des folgenden Codes:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Um ein Nebenläufigkeitsproblem zu erkennen, wird die [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)-Eigenschaft mit dem `rowVersion`-Wert aus der Entität aktualisiert, aus der dieser abgerufen wurde. EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert. Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

Im obenstehenden Code wird der Wert `Department.RowVersion` zurückgegeben, sobald die Entität abgerufen wurde. `OriginalValue` ist der Wert in der Datenbank, wenn `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde.

Der folgende Code ruft die Clientwerte (die für diese Methode bereitgestellten Werte) und die Datenbankwerte ab:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die auf `OnPostAsync` bereitgestellt wurden:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt. Auf der Razor Page hat der `ModelState`-Wert eines Felds Vorrang gegenüber den Modelleigenschaftswerten, wenn beide vorhanden sind.

## <a name="update-the-edit-page"></a>Aktualisieren der Seite „Bearbeiten“

Aktualisieren Sie *Pages/Courses/Edit.cshtml* mithilfe des folgenden Markups:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Das obenstehende Markup:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.
* Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.
* Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“

Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.
* Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Ändern Sie ein anderes Feld in der zweiten Registerkarte.

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

Klicken Sie auf **Speichern**. Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

In diesem Browserfenster sollte nicht das Namensfeld geändert werden. Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein. Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/cv.png)

Klicken Sie erneut auf **Speichern**. Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert. Die gespeicherten Werte werden auf der Indexseite angezeigt.

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie das Seitenmodell „Löschen“ mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde. Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde. Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`. Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:

* Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.
* Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.
* Wird `OnGetAsync` mit `concurrencyError` aufgerufen.

### <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Das oben stehende Markup führt die folgenden Änderungen durch:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Eine Fehlermeldung wird hinzugefügt.
* „FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.
* `RowVersion` wird geändert, um das letzte Byte anzuzeigen.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Überprüfen von Nebenläufigkeitskonflikten mit der Seite „Löschen“

Erstellen Sie einen Fachbereich zum Testen.

Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf**In neuer Registerkarte öffnen**.
* Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt. Klicken Sie auf **Löschen**, wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert und der Fachbereich wurde gelöscht.

Informationen zum Vererben eines Datenmodells finden Sie unter [Vererbung](xref:data/ef-mvc/inheritance).

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)](/ef/core/modeling/concurrency)
* [Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)](/ef/core/saving/concurrency)
* [Dieses Tutorial auf YouTube (Behandlung von Parallelitätskonflikten)](https://youtu.be/EosxHTFgYps)
* [Dieses Tutorial auf YouTube (Teil 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Dieses Tutorial auf YouTube (Teil 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Vorherige](xref:data/ef-rp/update-related-data)
