---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Parallelität (8 von 8)'
author: rick-anderson
description: In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2018
uid: data/ef-rp/concurrency
ms.openlocfilehash: 17ce0c111daabe2c7bbf4795b658856568c85158
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886175"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="b4e1f-103">Razor-Seiten mit EF Core in ASP.NET Core: Parallelität (8 von 8)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="b4e1f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) und [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="b4e1f-105">Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="b4e1f-106">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b4e1f-107">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="b4e1f-108">Nebenläufigkeitskonflikte</span><span class="sxs-lookup"><span data-stu-id="b4e1f-108">Concurrency conflicts</span></span>

<span data-ttu-id="b4e1f-109">Ein Nebenläufigkeitskonflikt tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-109">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="b4e1f-110">Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-110">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="b4e1f-111">Ein anderer Benutzer dieselbe Entität aktualisiert, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-111">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="b4e1f-112">Wenn die Parallelitätserkennung nicht aktiviert ist, während gleichzeitige Updates ausgeführt werden, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-112">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="b4e1f-113">Das letzte Update gilt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-113">The last update wins.</span></span> <span data-ttu-id="b4e1f-114">Das bedeutet, dass die neuesten zu aktualisierenden Werte in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-114">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="b4e1f-115">Das erste der aktuellen Updates geht verloren.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-115">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="b4e1f-116">Optimistische Nebenläufigkeit</span><span class="sxs-lookup"><span data-stu-id="b4e1f-116">Optimistic concurrency</span></span>

<span data-ttu-id="b4e1f-117">Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-117">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="b4e1f-118">Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-118">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

<span data-ttu-id="b4e1f-120">Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-120">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

<span data-ttu-id="b4e1f-122">Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn im Browser die Indexseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-122">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

<span data-ttu-id="b4e1f-124">Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-124">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="b4e1f-125">Was daraufhin geschieht ist abhängig davon, wie Sie Nebenläufigkeitskonflikte handhaben.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-125">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="b4e1f-126">Die optimistische Nebenläufigkeit umfasst die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-126">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="b4e1f-127">Sie können Nachverfolgen, welche Eigenschaft ein Benutzer geändert hat, und nur die entsprechenden Spalten in der Datenbank aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-127">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="b4e1f-128">In diesem Szenario sollten keine Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-128">In the scenario, no data would be lost.</span></span> <span data-ttu-id="b4e1f-129">Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-129">Different properties were updated by the two users.</span></span> <span data-ttu-id="b4e1f-130">Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-130">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="b4e1f-131">Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-131">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="b4e1f-132">Dieser Ansatz:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-132">This approach:</span></span>
 
  * <span data-ttu-id="b4e1f-133">Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-133">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="b4e1f-134">Ist in einer Web-App in der Regel nicht praktisch.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-134">Is generally not practical in a web app.</span></span> <span data-ttu-id="b4e1f-135">Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-135">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="b4e1f-136">Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-136">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="b4e1f-137">Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-137">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="b4e1f-138">Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-138">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="b4e1f-139">Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-139">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="b4e1f-140">Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-140">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="b4e1f-141">(Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-141">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="b4e1f-142">Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-142">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="b4e1f-143">In der Regel würde die App:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-143">Typically, the app would:</span></span>

  * <span data-ttu-id="b4e1f-144">Eine Fehlermeldung anzeigen</span><span class="sxs-lookup"><span data-stu-id="b4e1f-144">Display an error message.</span></span>
  * <span data-ttu-id="b4e1f-145">Den aktuellen Datenstatus anzeigen</span><span class="sxs-lookup"><span data-stu-id="b4e1f-145">Show the current state of the data.</span></span>
  * <span data-ttu-id="b4e1f-146">Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden</span><span class="sxs-lookup"><span data-stu-id="b4e1f-146">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="b4e1f-147">Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-147">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="b4e1f-148">(Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt).</span><span class="sxs-lookup"><span data-stu-id="b4e1f-148">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="b4e1f-149">Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-149">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="b4e1f-150">Behandlung von Parallelität</span><span class="sxs-lookup"><span data-stu-id="b4e1f-150">Handling concurrency</span></span> 

<span data-ttu-id="b4e1f-151">Wenn eine Eigenschaft als ein [Parallelitätstoken](/ef/core/modeling/concurrency) konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-151">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="b4e1f-152">Stellt EF Core sicher, dass die Eigenschaft nicht geändert wurde, nachdem sie abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-152">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="b4e1f-153">Die Überprüfung findet statt, wenn [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) oder [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-153">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="b4e1f-154">Wenn die Eigenschaft geändert wurde, nachdem sie abgerufen wurde, wird eine [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-154">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="b4e1f-155">Das Datenbank- und Datenmodell müssen konfiguriert sein, um das Auslösen von `DbUpdateConcurrencyException` zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-155">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="b4e1f-156">Erkennen von Nebenläufigkeitskonflikten mit Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="b4e1f-156">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="b4e1f-157">Nebenläufigkeitskonflikte können auf der Eigenschaftenebene über das [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0)-Attribut erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-157">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="b4e1f-158">Das Attribut kann auf mehrere Eigenschaften für das Modell angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-158">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="b4e1f-159">Weitere Informationen finden Sie unter [Datenanmerkungen-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b4e1f-159">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="b4e1f-160">Das Attribut `[ConcurrencyCheck]` wird in diesem Tutorial nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-160">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="b4e1f-161">Erkennen von Nebenläufigkeitskonflikten mit einer Zeile</span><span class="sxs-lookup"><span data-stu-id="b4e1f-161">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="b4e1f-162">Um Nebenläufigkeitskonflikte zu erkennen, wird dem Modell eine [Rowversion](/sql/t-sql/data-types/rowversion-transact-sql)-Nachverfolgungsspalte (Zeilenversion) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-162">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="b4e1f-163">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="b4e1f-163">`rowversion` :</span></span>

* <span data-ttu-id="b4e1f-164">Ist SQL Server-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-164">Is SQL Server specific.</span></span> <span data-ttu-id="b4e1f-165">Andere Datenbanken enthalten möglicherweise keine ähnlichen Features.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-165">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="b4e1f-166">Wird verwendet, um zu bestimmen, dass eine Entität, seit dem Abruf aus der Datenbank, nicht geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-166">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="b4e1f-167">Die Datenbank generiert eine sequenzielle Anzahl von `rowversion`, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-167">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="b4e1f-168">In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert von `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-168">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="b4e1f-169">Wenn sich die aktualisierte Zeile geändert hat, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-169">If the row being updated has changed:</span></span>

* <span data-ttu-id="b4e1f-170">`rowversion` entspricht nicht dem abgerufenen Wert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-170">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="b4e1f-171">Die Befehle `Update` oder `Delete` finden keine Zeile, da die `Where`-Klausel die abgerufene `rowversion` enthält.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-171">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="b4e1f-172">Es wird eine `DbUpdateConcurrencyException` ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-172">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="b4e1f-173">In EF Core wird eine Parallelitätsausnahme ausgelöst, wenn keine Zeilen durch einen `Update`- oder `Delete`-Befehl aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-173">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="b4e1f-174">Hinzufügen einer Nachverfolgungseigenschaft zur Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="b4e1f-174">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="b4e1f-175">Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-175">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="b4e1f-176">Das [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute)-Attribut gibt an, dass diese Spalte in der `Where`-Klausel der Befehle `Update` und `Delete` enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-176">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="b4e1f-177">Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor dieser durch SQL-`rowversion` ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-177">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="b4e1f-178">Die Fluent-API kann auch die Nachverfolgungseigenschaft angeben:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-178">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="b4e1f-179">Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-179">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

<span data-ttu-id="b4e1f-180">Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-180">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="b4e1f-181">Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-181">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="b4e1f-182">Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-182">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

<span data-ttu-id="b4e1f-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="b4e1f-184">Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-184">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="b4e1f-185">Sie können das von EF Core generierte T-SQL im Ausgabefenster von Visual Studio sehen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-185">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="b4e1f-186">Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="b4e1f-186">Update the DB</span></span>

<span data-ttu-id="b4e1f-187">Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-187">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="b4e1f-188">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-188">Build the project.</span></span> <span data-ttu-id="b4e1f-189">Geben Sie Folgendes in ein Befehlsfenster ein:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-189">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="b4e1f-190">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-190">The preceding commands:</span></span>

* <span data-ttu-id="b4e1f-191">Die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs* wird hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-191">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="b4e1f-192">Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-192">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="b4e1f-193">Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-193">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* <span data-ttu-id="b4e1f-194">Migrationen werden durchgeführt, um die Datenbank zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-194">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="b4e1f-195">Erstellen des Gerüsts für das Abteilungsmodell</span><span class="sxs-lookup"><span data-stu-id="b4e1f-195">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4e1f-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4e1f-196">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b4e1f-197">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Department` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-197">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Department` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b4e1f-198">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b4e1f-198">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="b4e1f-199">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-199">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="b4e1f-200">Der vorherige Befehl erstellt ein Gerüst für das `Department`-Modell.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-200">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="b4e1f-201">Öffnen Sie das Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-201">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b4e1f-202">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-202">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="b4e1f-203">Aktualisieren der Indexseite für Abteilungen</span><span class="sxs-lookup"><span data-stu-id="b4e1f-203">Update the Departments Index page</span></span>

<span data-ttu-id="b4e1f-204">Die Gerüstbauengine hat eine `RowVersion`-Spalte auf der Indexseite erstellt, jedoch sollte dieses Feld nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-204">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="b4e1f-205">In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis über die Parallelität erlangen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-205">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="b4e1f-206">Das letzte Byte ist nicht zwingend eindeutig.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-206">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="b4e1f-207">Eine echte App würde `RowVersion` oder das letzte Byte von `RowVersion` nicht anzeigen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-207">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="b4e1f-208">Aktualisieren der Indexseite:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-208">Update the Index page:</span></span>

* <span data-ttu-id="b4e1f-209">Ersetzen Sie „Index“ durch „Abteilungen“.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-209">Replace Index with Departments.</span></span>
* <span data-ttu-id="b4e1f-210">Ersetzen Sie das Markup mit `RowVersion` durch das letzten Byte von `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-210">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="b4e1f-211">Ersetzen Sie „FirstMidName“durch „FullName“.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-211">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="b4e1f-212">Das folgende Markup zeigt die aktualisierte Seite an:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-212">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="b4e1f-213">Aktualisieren des Seitenbearbeitungsmodells</span><span class="sxs-lookup"><span data-stu-id="b4e1f-213">Update the Edit page model</span></span>

<span data-ttu-id="b4e1f-214">Aktualisieren Sie die *pages\departments\edit.cshtml.cs*-Datei mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-214">Update *pages\departments\edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="b4e1f-215">Um ein Nebenläufigkeitsproblem zu erkennen, wird die [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)-Eigenschaft mit dem `rowVersion`-Wert aus der Entität aktualisiert, aus der dieser abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-215">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="b4e1f-216">EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-216">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="b4e1f-217">Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-217">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="b4e1f-218">Im obenstehenden Code wird der Wert `Department.RowVersion` zurückgegeben, sobald die Entität abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-218">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="b4e1f-219">`OriginalValue` ist der Wert in der Datenbank, wenn `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-219">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="b4e1f-220">Der folgende Code ruft die Clientwerte (die für diese Methode bereitgestellten Werte) und die Datenbankwerte ab:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-220">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="b4e1f-221">Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die auf `OnPostAsync` bereitgestellt wurden:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-221">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="b4e1f-222">Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-222">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="b4e1f-223">Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-223">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="b4e1f-224">Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-224">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="b4e1f-225">Auf der Razor Page hat der `ModelState`-Wert eines Felds Vorrang gegenüber den Modelleigenschaftswerten, wenn beide vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-225">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="b4e1f-226">Aktualisieren der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="b4e1f-226">Update the Edit page</span></span>

<span data-ttu-id="b4e1f-227">Aktualisieren Sie *Pages/Courses/Edit.cshtml* mithilfe des folgenden Markups:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-227">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="b4e1f-228">Das obenstehende Markup:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-228">The preceding markup:</span></span>

* <span data-ttu-id="b4e1f-229">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-229">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="b4e1f-230">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-230">Adds a hidden row version.</span></span> <span data-ttu-id="b4e1f-231">`RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-231">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="b4e1f-232">Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-232">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="b4e1f-233">Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-233">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="b4e1f-234">Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="b4e1f-234">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="b4e1f-235">Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-235">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="b4e1f-236">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-236">Run the app and select Departments.</span></span>
* <span data-ttu-id="b4e1f-237">Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-237">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="b4e1f-238">Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-238">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="b4e1f-239">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-239">The two browser tabs display the same information.</span></span>

<span data-ttu-id="b4e1f-240">Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-240">Change the name in the first browser tab and click **Save**.</span></span>

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="b4e1f-242">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-242">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="b4e1f-243">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-243">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="b4e1f-244">Ändern Sie ein anderes Feld in der zweiten Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-244">Change a different field in the second browser tab.</span></span>

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="b4e1f-246">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-246">Click **Save**.</span></span> <span data-ttu-id="b4e1f-247">Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-247">You see error messages for all fields that don't match the DB values:</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

<span data-ttu-id="b4e1f-249">In diesem Browserfenster sollte nicht das Namensfeld geändert werden.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-249">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="b4e1f-250">Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-250">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="b4e1f-251">Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-251">Tab out. Client-side validation removes the error message.</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/cv.png)

<span data-ttu-id="b4e1f-253">Klicken Sie erneut auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-253">Click **Save** again.</span></span> <span data-ttu-id="b4e1f-254">Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-254">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="b4e1f-255">Die gespeicherten Werte werden auf der Indexseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-255">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="b4e1f-256">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-256">Update the Delete page</span></span>

<span data-ttu-id="b4e1f-257">Aktualisieren Sie das Seitenmodell „Löschen“ mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-257">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="b4e1f-258">Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-258">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="b4e1f-259">Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-259">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="b4e1f-260">Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-260">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="b4e1f-261">Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-261">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="b4e1f-262">Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-262">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="b4e1f-263">Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-263">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="b4e1f-264">Wird `OnGetAsync` mit `concurrencyError` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-264">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="b4e1f-265">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-265">Update the Delete page</span></span>

<span data-ttu-id="b4e1f-266">Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-266">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="b4e1f-267">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-267">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b4e1f-268">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-268">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="b4e1f-269">Eine Fehlermeldung wird hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-269">Adds an error message.</span></span>
* <span data-ttu-id="b4e1f-270">„FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-270">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="b4e1f-271">`RowVersion` wird geändert, um das letzte Byte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-271">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="b4e1f-272">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-272">Adds a hidden row version.</span></span> <span data-ttu-id="b4e1f-273">`RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-273">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="b4e1f-274">Überprüfen von Nebenläufigkeitskonflikten mit der Seite „Löschen“</span><span class="sxs-lookup"><span data-stu-id="b4e1f-274">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="b4e1f-275">Erstellen Sie einen Fachbereich zum Testen.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-275">Create a test department.</span></span>

<span data-ttu-id="b4e1f-276">Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:</span><span class="sxs-lookup"><span data-stu-id="b4e1f-276">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="b4e1f-277">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-277">Run the app and select Departments.</span></span>
* <span data-ttu-id="b4e1f-278">Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf**In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-278">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="b4e1f-279">Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-279">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="b4e1f-280">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-280">The two browser tabs display the same information.</span></span>

<span data-ttu-id="b4e1f-281">Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-281">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="b4e1f-282">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-282">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="b4e1f-283">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-283">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="b4e1f-284">Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-284">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="b4e1f-285">Klicken Sie auf **Löschen**, wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert und der Fachbereich wurde gelöscht.</span><span class="sxs-lookup"><span data-stu-id="b4e1f-285">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="b4e1f-286">Informationen zum Vererben eines Datenmodells finden Sie unter [Vererbung](xref:data/ef-mvc/inheritance).</span><span class="sxs-lookup"><span data-stu-id="b4e1f-286">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="b4e1f-287">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b4e1f-287">Additional resources</span></span>

* [<span data-ttu-id="b4e1f-288">Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-288">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="b4e1f-289">Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-289">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="b4e1f-290">Dieses Tutorial auf YouTube (Behandlung von Parallelitätskonflikten)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-290">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="b4e1f-291">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-291">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="b4e1f-292">Dieses Tutorial auf YouTube (Teil 3)</span><span class="sxs-lookup"><span data-stu-id="b4e1f-292">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="b4e1f-293">Vorherige</span><span class="sxs-lookup"><span data-stu-id="b4e1f-293">Previous</span></span>](xref:data/ef-rp/update-related-data)
