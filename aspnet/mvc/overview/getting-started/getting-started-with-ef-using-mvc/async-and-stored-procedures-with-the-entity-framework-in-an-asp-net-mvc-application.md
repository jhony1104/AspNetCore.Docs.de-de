---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Verwenden Sie asynchrone und gespeicherte Prozeduren mit EF in einer ASP.NET MVC-App'
description: In diesem Tutorial erfahren Sie, wie das asynchrone Programmiermodell implementieren, und erfahren, wie gespeicherte Prozeduren verwenden.
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9041167af076d80ebf294e054ffe51293d11e888
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889872"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="218c7-103">Tutorial: Verwenden Sie asynchrone und gespeicherte Prozeduren mit EF in einer ASP.NET MVC-App</span><span class="sxs-lookup"><span data-stu-id="218c7-103">Tutorial: Use async and stored procedures with EF in an ASP.NET MVC App</span></span>

<span data-ttu-id="218c7-104">In den vorherigen Tutorials haben Sie gelernt, wie zum Lesen und Aktualisieren von Daten mit dem synchronen Programmiermodell wird.</span><span class="sxs-lookup"><span data-stu-id="218c7-104">In earlier tutorials you learned how to read and update data using the synchronous programming model.</span></span> <span data-ttu-id="218c7-105">In diesem Tutorial erfahren Sie, wie das asynchrone Programmiermodell implementieren.</span><span class="sxs-lookup"><span data-stu-id="218c7-105">In this tutorial you see how to implement the asynchronous programming model.</span></span> <span data-ttu-id="218c7-106">Asynchroner Code kann helfen, eine Anwendung, die besser ausgeführt werden, da es sich um eine bessere Verwendung von Serverressourcen ist.</span><span class="sxs-lookup"><span data-stu-id="218c7-106">Asynchronous code can help an application perform better because it makes better use of server resources.</span></span>

<span data-ttu-id="218c7-107">In diesem Tutorial sehen Sie auch die Verwendung von gespeicherten Prozeduren für Einfüge-, Update- und Delete-Vorgänge für eine Entität.</span><span class="sxs-lookup"><span data-stu-id="218c7-107">In this tutorial you also see how to use stored procedures for insert, update, and delete operations on an entity.</span></span>

<span data-ttu-id="218c7-108">Abschließend stellen Sie die Anwendung in Azure, zusammen mit all den Änderungen in der Datenbank, die Sie seit der ersten implementiert haben, die Sie bereitgestellt erneut bereit.</span><span class="sxs-lookup"><span data-stu-id="218c7-108">Finally, you redeploy the application to Azure, along with all of the database changes that you've implemented since the first time you deployed.</span></span>

<span data-ttu-id="218c7-109">In den folgenden Abbildungen werden die Seiten dargestellt, mit denen Sie arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="218c7-109">The following illustrations show some of the pages that you'll work with.</span></span>

![Seite "Abteilungen"](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Erstellen Sie die Abteilung](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="218c7-112">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="218c7-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="218c7-113">Erfahren Sie mehr über den asynchronen code</span><span class="sxs-lookup"><span data-stu-id="218c7-113">Learn about asynchronous code</span></span>
> * <span data-ttu-id="218c7-114">Erstellen Sie einen Controller für die Abteilung</span><span class="sxs-lookup"><span data-stu-id="218c7-114">Create a Department controller</span></span>
> * <span data-ttu-id="218c7-115">Verwenden von gespeicherten Prozeduren</span><span class="sxs-lookup"><span data-stu-id="218c7-115">Use stored procedures</span></span>
> * <span data-ttu-id="218c7-116">Bereitstellung in Azure</span><span class="sxs-lookup"><span data-stu-id="218c7-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="218c7-117">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="218c7-117">Prerequisites</span></span>

* [<span data-ttu-id="218c7-118">Aktualisieren von relevanten Daten</span><span class="sxs-lookup"><span data-stu-id="218c7-118">Updating Related Data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a><span data-ttu-id="218c7-119">Gründe für die Verwendung von asynchronen Codes</span><span class="sxs-lookup"><span data-stu-id="218c7-119">Why use asynchronous code</span></span>

<span data-ttu-id="218c7-120">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="218c7-120">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="218c7-121">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="218c7-121">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="218c7-122">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="218c7-122">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="218c7-123">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="218c7-123">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="218c7-124">Asynchroner Code ermöglicht daher Serverressourcen effizienter, und der Server ist aktiviert, um mehr Datenverkehr ohne Verzögerungen zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="218c7-124">As a result, asynchronous code enables server resources to be use more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="218c7-125">In früheren Versionen von .NET schreiben und Testen von asynchronem Code war komplex, fehleranfällig und schwierig zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="218c7-125">In earlier versions of .NET, writing and testing asynchronous code was complex, error prone, and hard to debug.</span></span> <span data-ttu-id="218c7-126">In .NET 4.5 ist schreiben, testen und Debuggen von asynchronem Code sehr viel einfacher, in der Regel von asynchronen Code geschrieben werden soll, wenn Sie einen Grund nicht zu haben.</span><span class="sxs-lookup"><span data-stu-id="218c7-126">In .NET 4.5, writing, testing, and debugging asynchronous code is so much easier that you should generally write asynchronous code unless you have a reason not to.</span></span> <span data-ttu-id="218c7-127">Asynchronem Code entsteht eine kleine Menge an Mehraufwand, aber in Situationen mit wenig Datenverkehr die Leistungseinbußen bei verarbeitet für Situationen mit hohem Datenverkehr wird, die mögliche leistungsverbesserung beträchtliche ist.</span><span class="sxs-lookup"><span data-stu-id="218c7-127">Asynchronous code does introduce a small amount of overhead, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="218c7-128">Weitere Informationen zur asynchronen Programmierung finden Sie unter [verwenden .NET 4.5-Async-Unterstützung, die Vermeidung von blockierenden Aufrufen](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span><span class="sxs-lookup"><span data-stu-id="218c7-128">For more information about asynchronous programming, see [Use .NET 4.5's async support to avoid blocking calls](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span></span>

## <a name="create-department-controller"></a><span data-ttu-id="218c7-129">Erstellen des abteilungscontrollers</span><span class="sxs-lookup"><span data-stu-id="218c7-129">Create Department controller</span></span>

<span data-ttu-id="218c7-130">Erstellen Sie eine abteilungscontrollers, wählen Sie die gleiche Weise wie die früheren Controller, außer dass diesmal, die **Async-Controlleraktionen verwenden** Kontrollkästchen.</span><span class="sxs-lookup"><span data-stu-id="218c7-130">Create a Department controller the same way you did the earlier controllers, except this time select the **Use async controller actions** check box.</span></span>

<span data-ttu-id="218c7-131">Die folgenden Besonderheiten zeigen an, was auf den synchronen Code für hinzugefügt wurde die `Index` Methode, um es asynchron auszuführen:</span><span class="sxs-lookup"><span data-stu-id="218c7-131">The following highlights show what was added to the synchronous code for the `Index` method to make it asynchronous:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

<span data-ttu-id="218c7-132">Vier Änderungen wurden angewendet, sodass die Entity Framework-Datenbankabfrage, der asynchron ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="218c7-132">Four changes were applied to enable the Entity Framework database query to execute asynchronously:</span></span>

- <span data-ttu-id="218c7-133">Die Methode ist mit markiert die `async` -Schlüsselwort, das weist den Compiler an, um Rückrufe für Teile des Methodentexts generieren und zum automatischen Erstellen der `Task<ActionResult>` -Objekt, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="218c7-133">The method is marked with the `async` keyword, which tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<ActionResult>` object that is returned.</span></span>
- <span data-ttu-id="218c7-134">Der Rückgabetyp geändert wurde, aus `ActionResult` zu `Task<ActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="218c7-134">The return type was changed from `ActionResult` to `Task<ActionResult>`.</span></span> <span data-ttu-id="218c7-135">Die `Task<T>` Typ stellt derzeit ausgeführte Arbeiten mit einem Ergebnis vom Typ `T`.</span><span class="sxs-lookup"><span data-stu-id="218c7-135">The `Task<T>` type represents ongoing work with a result of type `T`.</span></span>
- <span data-ttu-id="218c7-136">Die `await` -Schlüsselwort auf den Aufruf des Webdiensts angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="218c7-136">The `await` keyword was applied to the web service call.</span></span> <span data-ttu-id="218c7-137">Wenn der Compiler dieses Schlüsselwort erkennt, teilt hinter den Kulissen es die Methode in zwei Teile.</span><span class="sxs-lookup"><span data-stu-id="218c7-137">When the compiler sees this keyword, behind the scenes it splits the method into two parts.</span></span> <span data-ttu-id="218c7-138">Der erste Teil endet mit dem Vorgang, der asynchron gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="218c7-138">The first part ends with the operation that is started asynchronously.</span></span> <span data-ttu-id="218c7-139">Der zweite Teil ist eine Rückrufmethode abgelegt, die aufgerufen wird, wenn der Vorgang abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="218c7-139">The second part is put into a callback method that is called when the operation completes.</span></span>
- <span data-ttu-id="218c7-140">Die asynchrone Version von der `ToList` Erweiterungsmethode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="218c7-140">The asynchronous version of the `ToList` extension method was called.</span></span>

<span data-ttu-id="218c7-141">Warum ist die `departments.ToList` -Anweisung geändert, aber nicht die `departments = db.Departments` Anweisung?</span><span class="sxs-lookup"><span data-stu-id="218c7-141">Why is the `departments.ToList` statement modified but not the `departments = db.Departments` statement?</span></span> <span data-ttu-id="218c7-142">Der Grund ist, dass nur die Anweisungen, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden asynchron ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="218c7-142">The reason is that only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="218c7-143">Die `departments = db.Departments` Anweisung richtet eine Abfrage, aber die Abfrage wird nicht ausgeführt, bis die `ToList` Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="218c7-143">The `departments = db.Departments` statement sets up a query but the query is not executed until the `ToList` method is called.</span></span> <span data-ttu-id="218c7-144">Aus diesem Grund nur die `ToList` Methode asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="218c7-144">Therefore, only the `ToList` method is executed asynchronously.</span></span>

<span data-ttu-id="218c7-145">In der `Details` Methode und die `HttpGet` `Edit` und `Delete` Methoden, die `Find` Methode ist diejenige, die bewirkt, dass eine Abfrage an die Datenbank gesendet werden, ist die Methode, die asynchron ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="218c7-145">In the `Details` method and the `HttpGet` `Edit` and `Delete` methods, the `Find` method is the one that causes a query to be sent to the database, so that's the method that gets executed asynchronously:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

<span data-ttu-id="218c7-146">In der `Create`, `HttpPost Edit`, und `DeleteConfirmed` Methoden ist die `SaveChanges` Methodenaufruf, der bewirkt, dass einen Befehl ausgeführt wird, nicht auf Anweisungen wie `db.Departments.Add(department)` der nur dazu führen, dass Entitäten im Arbeitsspeicher geändert werden.</span><span class="sxs-lookup"><span data-stu-id="218c7-146">In the `Create`, `HttpPost Edit`, and `DeleteConfirmed` methods, it is the `SaveChanges` method call that causes a command to be executed, not statements such as `db.Departments.Add(department)` which only cause entities in memory to be modified.</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

<span data-ttu-id="218c7-147">Open *Views\Department\Index.cshtml*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="218c7-147">Open *Views\Department\Index.cshtml*, and replace the template code with the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

<span data-ttu-id="218c7-148">Dieser Code ändert den Titel aus dem Index für Abteilungen, bewegen Sie den Namen des Administrators nach rechts, und stellt den vollständigen Namen des Administrators.</span><span class="sxs-lookup"><span data-stu-id="218c7-148">This code changes the title from Index to Departments, moves the Administrator name to the right, and provides the full name of the administrator.</span></span>

<span data-ttu-id="218c7-149">Erstellen, löschen "," Details ", und Bearbeiten von Ansichten, die Beschriftung für die `InstructorID` Feld"Administrator"die gleiche Weise, wie Sie das Feld" Department Name ","Department"in der kursansichten geändert.</span><span class="sxs-lookup"><span data-stu-id="218c7-149">In the Create, Delete, Details, and Edit views, change the caption for the `InstructorID` field to "Administrator" the same way you changed the department name field to "Department" in the Course views.</span></span>

<span data-ttu-id="218c7-150">Verwenden Sie in das Erstellen und bearbeiten Ansichten den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="218c7-150">In the Create and Edit views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

<span data-ttu-id="218c7-151">Verwenden Sie in den Ansichten löschen und Details den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="218c7-151">In the Delete and Details views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

<span data-ttu-id="218c7-152">Führen Sie die Anwendung, und klicken Sie auf die **Abteilungen** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="218c7-152">Run the application, and click the **Departments** tab.</span></span>

<span data-ttu-id="218c7-153">Alles funktioniert genauso wie bei anderen Controller, aber in diesem Controller alle SQL-Abfragen asynchron ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="218c7-153">Everything works the same as in the other controllers, but in this controller all of the SQL queries are executing asynchronously.</span></span>

<span data-ttu-id="218c7-154">Einige Aspekte zu beachten, wenn Sie die asynchronen Programmierung mit dem Entity Framework verwenden:</span><span class="sxs-lookup"><span data-stu-id="218c7-154">Some things to be aware of when you are using asynchronous programming with the Entity Framework:</span></span>

- <span data-ttu-id="218c7-155">Die Async-Code ist nicht threadsicher.</span><span class="sxs-lookup"><span data-stu-id="218c7-155">The async code is not thread safe.</span></span> <span data-ttu-id="218c7-156">Das heißt, das heißt, versuchen Sie nicht für mehrere Vorgänge parallel mit der gleichen Kontextinstanz.</span><span class="sxs-lookup"><span data-stu-id="218c7-156">In other words, in other words, don't try to do multiple operations in parallel using the same context instance.</span></span>
- <span data-ttu-id="218c7-157">Wenn Sie von den Leistungsvorteilen des asynchronen Codes profitieren möchten, vergewissern Sie sich, dass auch alle Bibliothekspakete, die Sie verwenden (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="218c7-157">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

## <a name="use-stored-procedures"></a><span data-ttu-id="218c7-158">Verwenden von gespeicherten Prozeduren</span><span class="sxs-lookup"><span data-stu-id="218c7-158">Use stored procedures</span></span>

<span data-ttu-id="218c7-159">Einige Entwickler und Datenbankadministratoren möchten gespeicherte Prozeduren für den Zugriff auf die Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="218c7-159">Some developers and DBAs prefer to use stored procedures for database access.</span></span> <span data-ttu-id="218c7-160">In früheren Versionen von Entity Framework können Sie Daten mithilfe einer gespeicherten Prozedur durch Abrufen [eine unformatierte SQL-Abfrage ausführen](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), aber Sie können keine anweisen, EF, gespeicherte Prozeduren für Updatevorgänge verwendet.</span><span class="sxs-lookup"><span data-stu-id="218c7-160">In earlier versions of Entity Framework you can retrieve data using a stored procedure by [executing a raw SQL query](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), but you can't instruct EF to use stored procedures for update operations.</span></span> <span data-ttu-id="218c7-161">In EF 6 ist es einfach, Code First zur Verwendung gespeicherter Prozeduren konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="218c7-161">In EF 6 it's easy to configure Code First to use stored procedures.</span></span>

1. <span data-ttu-id="218c7-162">In *DAL\SchoolContext.cs*, fügen Sie den hervorgehobenen Code die `OnModelCreating` Methode.</span><span class="sxs-lookup"><span data-stu-id="218c7-162">In *DAL\SchoolContext.cs*, add the highlighted code to the `OnModelCreating` method.</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    <span data-ttu-id="218c7-163">Dieser Code weist die Entity Framework verwenden Sie gespeicherte Prozeduren für Einfüge-, update und delete-Operationen auf die `Department` Entität.</span><span class="sxs-lookup"><span data-stu-id="218c7-163">This code instructs Entity Framework to use stored procedures for insert, update, and delete operations on the `Department` entity.</span></span>
2. <span data-ttu-id="218c7-164">Geben Sie im Paketkonsole verwalten den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="218c7-164">In Package Manage Console, enter the following command:</span></span>

    `add-migration DepartmentSP`

    <span data-ttu-id="218c7-165">Open *Migrationen\&Lt; Zeitstempel&gt;\_DepartmentSP.cs* , wie der Code in die `Up` Methode, die erstellt INSERT-, Update- und Delete gespeicherte Prozeduren:</span><span class="sxs-lookup"><span data-stu-id="218c7-165">Open *Migrations\&lt;timestamp&gt;\_DepartmentSP.cs* to see the code in the `Up` method that creates Insert, Update, and Delete stored procedures:</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. <span data-ttu-id="218c7-166">Geben Sie im Paketkonsole verwalten den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="218c7-166">In Package Manage Console, enter the following command:</span></span>

     `update-database`
4. <span data-ttu-id="218c7-167">Führen Sie die Anwendung im Debugmodus befindet, klicken Sie auf die **Abteilungen** Registerkarte, und klicken Sie dann auf **neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="218c7-167">Run the application in debug mode, click the **Departments** tab, and then click **Create New**.</span></span>
5. <span data-ttu-id="218c7-168">Geben Sie Daten für eine neue Abteilung, und klicken Sie dann auf **erstellen**.</span><span class="sxs-lookup"><span data-stu-id="218c7-168">Enter data for a new department, and then click **Create**.</span></span>

6. <span data-ttu-id="218c7-169">In Visual Studio sehen Sie sich in den Protokollen der **Ausgabe** Fenster zu sehen, dass eine gespeicherte Prozedur verwendet wurde, um die neue Abteilung Zeile einzufügen.</span><span class="sxs-lookup"><span data-stu-id="218c7-169">In Visual Studio, look at the logs in the **Output** window to see that a stored procedure was used to insert the new Department row.</span></span>

     ![Abteilung Insert SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

<span data-ttu-id="218c7-171">Namen für Standardwerte, die gespeicherte Prozedur wird von Code First erstellt.</span><span class="sxs-lookup"><span data-stu-id="218c7-171">Code First creates default stored procedure names.</span></span> <span data-ttu-id="218c7-172">Wenn Sie eine vorhandene Datenbank verwenden, müssen Sie die Namen der gespeicherten Prozedur anpassen, um bereits in der Datenbank definierten, gespeicherten Prozeduren zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="218c7-172">If you are using an existing database, you might need to customize the stored procedure names in order to use stored procedures already defined in the database.</span></span> <span data-ttu-id="218c7-173">Informationen dazu, wie Sie dies tun, finden Sie unter [Entity Framework Code erste Insert/Update/Delete gespeicherte Prozeduren](https://msdn.microsoft.com/data/dn468673).</span><span class="sxs-lookup"><span data-stu-id="218c7-173">For information about how to do that, see [Entity Framework Code First Insert/Update/Delete Stored Procedures](https://msdn.microsoft.com/data/dn468673).</span></span>

<span data-ttu-id="218c7-174">Wenn anpassen, welche gespeicherten Prozeduren werden generiert werden sollen, können Sie bearbeiten, dass das Codegerüst für Migrationen `Up` -Methode, die die gespeicherte Prozedur erstellt.</span><span class="sxs-lookup"><span data-stu-id="218c7-174">If you want to customize what generated stored procedures do, you can edit the scaffolded code for the migrations `Up` method that creates the stored procedure.</span></span> <span data-ttu-id="218c7-175">Auf diese Weise werden die Änderungen jedes Mal, wenn wiedergegeben, dass die Migration wird ausgeführt und gelten für die Produktionsdatenbank bei Migrationen wird automatisch ausgeführt, in der Produktion nach der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="218c7-175">That way your changes are reflected whenever that migration is run and will be applied to your production database when migrations runs automatically in production after deployment.</span></span>

<span data-ttu-id="218c7-176">Sollten Sie eine vorhandene gespeicherte Prozedur zu ändern, die in einer vorherigen Migration erstellt wurde, können Sie verwenden Sie den Befehl Add-Migration, um eine leere Migration zu generieren und dann manuell Schreiben von Code, der Aufrufe der [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) Methode .</span><span class="sxs-lookup"><span data-stu-id="218c7-176">If you want to change an existing stored procedure that was created in a previous migration, you can use the Add-Migration command to generate a blank migration, and then manually write code that calls the [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) method.</span></span>

## <a name="deploy-to-azure"></a><span data-ttu-id="218c7-177">Bereitstellung in Azure</span><span class="sxs-lookup"><span data-stu-id="218c7-177">Deploy to Azure</span></span>

<span data-ttu-id="218c7-178">In diesem Abschnitt müssen Sie das optionale haben **Bereitstellen der app für Azure** im Abschnitt der [Migrationen und Bereitstellung](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) Tutorial dieser Reihe.</span><span class="sxs-lookup"><span data-stu-id="218c7-178">This section requires you to have completed the optional **Deploying the app to Azure** section in the [Migrations and Deployment](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial of this series.</span></span> <span data-ttu-id="218c7-179">Überspringen Sie diesen Abschnitt, wenn Sie Migrationen Fehler, die Sie enthält durch Löschen der Datenbank in Ihrem lokalen Projekt aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="218c7-179">If you had migrations errors that you resolved by deleting the database in your local project, skip this section.</span></span>

1. <span data-ttu-id="218c7-180">In Visual Studio mit der Maustaste des Projekts im **Projektmappen-Explorer** , und wählen Sie **veröffentlichen** aus dem Kontextmenü.</span><span class="sxs-lookup"><span data-stu-id="218c7-180">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>
2. <span data-ttu-id="218c7-181">Klicken Sie auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="218c7-181">Click **Publish**.</span></span>

    <span data-ttu-id="218c7-182">Visual Studio stellt die Anwendung in Azure bereit und öffnet die Anwendung in Ihrem Standardbrowser, die in Azure ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="218c7-182">Visual Studio deploys the application to Azure, and the application opens in your default browser, running in Azure.</span></span>
3. <span data-ttu-id="218c7-183">Testen Sie die Anwendung, überprüfen Sie, ob es funktioniert.</span><span class="sxs-lookup"><span data-stu-id="218c7-183">Test the application to verify it's working.</span></span>

    <span data-ttu-id="218c7-184">Beim ersten einer Seite ausführen, auf die Datenbank zugreift, Entity Framework führt alle Migrationen `Up` Methoden erforderlich, um die Datenbank mit dem aktuellen Datenmodell auf dem neuesten Stand zu bringen.</span><span class="sxs-lookup"><span data-stu-id="218c7-184">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span> <span data-ttu-id="218c7-185">Sie können jetzt alle Webseiten verwenden, die Sie seit dem letzten, die Sie bereitgestellt haben hinzugefügt, einschließlich der Abteilung-Seiten, die Sie in diesem Lernprogramm hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="218c7-185">You can now use all of the web pages that you added since the last time you deployed, including the Department pages that you added in this tutorial.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="218c7-186">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="218c7-186">Get the code</span></span>

[<span data-ttu-id="218c7-187">Herunterladen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="218c7-187">Download the Completed Project</span></span>](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a><span data-ttu-id="218c7-188">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="218c7-188">Additional resources</span></span>

<span data-ttu-id="218c7-189">Links zu anderen Ressourcen des Entity Framework finden Sie in der [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="218c7-189">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="218c7-190">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="218c7-190">Next steps</span></span>

<span data-ttu-id="218c7-191">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="218c7-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="218c7-192">Erfahren asynchronen code</span><span class="sxs-lookup"><span data-stu-id="218c7-192">Learned about asynchronous code</span></span>
> * <span data-ttu-id="218c7-193">Erstellt einen Controller für die Abteilung</span><span class="sxs-lookup"><span data-stu-id="218c7-193">Created a Department controller</span></span>
> * <span data-ttu-id="218c7-194">Gespeicherte Prozeduren verwendet</span><span class="sxs-lookup"><span data-stu-id="218c7-194">Used stored procedures</span></span>
> * <span data-ttu-id="218c7-195">In Azure bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="218c7-195">Deployed to Azure</span></span>

<span data-ttu-id="218c7-196">Wechseln Sie zum nächsten Artikel erfahren Sie, wie Sie Konflikte behandeln, wenn mehrere Benutzer dieselbe Entität gleichzeitig aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="218c7-196">Advance to the next article to learn how to handle conflicts when multiple users update the same entity at the same time.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="218c7-197">Behandeln von Parallelität</span><span class="sxs-lookup"><span data-stu-id="218c7-197">Handling concurrency</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
