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
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a>Tutorial: Verwenden Sie asynchrone und gespeicherte Prozeduren mit EF in einer ASP.NET MVC-App

In den vorherigen Tutorials haben Sie gelernt, wie zum Lesen und Aktualisieren von Daten mit dem synchronen Programmiermodell wird. In diesem Tutorial erfahren Sie, wie das asynchrone Programmiermodell implementieren. Asynchroner Code kann helfen, eine Anwendung, die besser ausgeführt werden, da es sich um eine bessere Verwendung von Serverressourcen ist.

In diesem Tutorial sehen Sie auch die Verwendung von gespeicherten Prozeduren für Einfüge-, Update- und Delete-Vorgänge für eine Entität.

Abschließend stellen Sie die Anwendung in Azure, zusammen mit all den Änderungen in der Datenbank, die Sie seit der ersten implementiert haben, die Sie bereitgestellt erneut bereit.

In den folgenden Abbildungen werden die Seiten dargestellt, mit denen Sie arbeiten werden.

![Seite "Abteilungen"](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Erstellen Sie die Abteilung](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren Sie mehr über den asynchronen code
> * Erstellen Sie einen Controller für die Abteilung
> * Verwenden von gespeicherten Prozeduren
> * Bereitstellung in Azure

## <a name="prerequisites"></a>Vorraussetzungen

* [Aktualisieren von relevanten Daten](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a>Gründe für die Verwendung von asynchronen Codes

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Asynchroner Code ermöglicht daher Serverressourcen effizienter, und der Server ist aktiviert, um mehr Datenverkehr ohne Verzögerungen zu behandeln.

In früheren Versionen von .NET schreiben und Testen von asynchronem Code war komplex, fehleranfällig und schwierig zu debuggen. In .NET 4.5 ist schreiben, testen und Debuggen von asynchronem Code sehr viel einfacher, in der Regel von asynchronen Code geschrieben werden soll, wenn Sie einen Grund nicht zu haben. Asynchronem Code entsteht eine kleine Menge an Mehraufwand, aber in Situationen mit wenig Datenverkehr die Leistungseinbußen bei verarbeitet für Situationen mit hohem Datenverkehr wird, die mögliche leistungsverbesserung beträchtliche ist.

Weitere Informationen zur asynchronen Programmierung finden Sie unter [verwenden .NET 4.5-Async-Unterstützung, die Vermeidung von blockierenden Aufrufen](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).

## <a name="create-department-controller"></a>Erstellen des abteilungscontrollers

Erstellen Sie eine abteilungscontrollers, wählen Sie die gleiche Weise wie die früheren Controller, außer dass diesmal, die **Async-Controlleraktionen verwenden** Kontrollkästchen.

Die folgenden Besonderheiten zeigen an, was auf den synchronen Code für hinzugefügt wurde die `Index` Methode, um es asynchron auszuführen:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

Vier Änderungen wurden angewendet, sodass die Entity Framework-Datenbankabfrage, der asynchron ausgeführt:

- Die Methode ist mit markiert die `async` -Schlüsselwort, das weist den Compiler an, um Rückrufe für Teile des Methodentexts generieren und zum automatischen Erstellen der `Task<ActionResult>` -Objekt, das zurückgegeben wird.
- Der Rückgabetyp geändert wurde, aus `ActionResult` zu `Task<ActionResult>`. Die `Task<T>` Typ stellt derzeit ausgeführte Arbeiten mit einem Ergebnis vom Typ `T`.
- Die `await` -Schlüsselwort auf den Aufruf des Webdiensts angewendet wurde. Wenn der Compiler dieses Schlüsselwort erkennt, teilt hinter den Kulissen es die Methode in zwei Teile. Der erste Teil endet mit dem Vorgang, der asynchron gestartet wird. Der zweite Teil ist eine Rückrufmethode abgelegt, die aufgerufen wird, wenn der Vorgang abgeschlossen ist.
- Die asynchrone Version von der `ToList` Erweiterungsmethode aufgerufen wurde.

Warum ist die `departments.ToList` -Anweisung geändert, aber nicht die `departments = db.Departments` Anweisung? Der Grund ist, dass nur die Anweisungen, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden asynchron ausgeführt werden. Die `departments = db.Departments` Anweisung richtet eine Abfrage, aber die Abfrage wird nicht ausgeführt, bis die `ToList` Methode wird aufgerufen. Aus diesem Grund nur die `ToList` Methode asynchron ausgeführt wird.

In der `Details` Methode und die `HttpGet` `Edit` und `Delete` Methoden, die `Find` Methode ist diejenige, die bewirkt, dass eine Abfrage an die Datenbank gesendet werden, ist die Methode, die asynchron ausgeführt wird:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

In der `Create`, `HttpPost Edit`, und `DeleteConfirmed` Methoden ist die `SaveChanges` Methodenaufruf, der bewirkt, dass einen Befehl ausgeführt wird, nicht auf Anweisungen wie `db.Departments.Add(department)` der nur dazu führen, dass Entitäten im Arbeitsspeicher geändert werden.

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

Open *Views\Department\Index.cshtml*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

Dieser Code ändert den Titel aus dem Index für Abteilungen, bewegen Sie den Namen des Administrators nach rechts, und stellt den vollständigen Namen des Administrators.

Erstellen, löschen "," Details ", und Bearbeiten von Ansichten, die Beschriftung für die `InstructorID` Feld"Administrator"die gleiche Weise, wie Sie das Feld" Department Name ","Department"in der kursansichten geändert.

Verwenden Sie in das Erstellen und bearbeiten Ansichten den folgenden Code:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

Verwenden Sie in den Ansichten löschen und Details den folgenden Code:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Führen Sie die Anwendung, und klicken Sie auf die **Abteilungen** Registerkarte.

Alles funktioniert genauso wie bei anderen Controller, aber in diesem Controller alle SQL-Abfragen asynchron ausgeführt werden.

Einige Aspekte zu beachten, wenn Sie die asynchronen Programmierung mit dem Entity Framework verwenden:

- Die Async-Code ist nicht threadsicher. Das heißt, das heißt, versuchen Sie nicht für mehrere Vorgänge parallel mit der gleichen Kontextinstanz.
- Wenn Sie von den Leistungsvorteilen des asynchronen Codes profitieren möchten, vergewissern Sie sich, dass auch alle Bibliothekspakete, die Sie verwenden (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

## <a name="use-stored-procedures"></a>Verwenden von gespeicherten Prozeduren

Einige Entwickler und Datenbankadministratoren möchten gespeicherte Prozeduren für den Zugriff auf die Datenbank verwenden. In früheren Versionen von Entity Framework können Sie Daten mithilfe einer gespeicherten Prozedur durch Abrufen [eine unformatierte SQL-Abfrage ausführen](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), aber Sie können keine anweisen, EF, gespeicherte Prozeduren für Updatevorgänge verwendet. In EF 6 ist es einfach, Code First zur Verwendung gespeicherter Prozeduren konfigurieren.

1. In *DAL\SchoolContext.cs*, fügen Sie den hervorgehobenen Code die `OnModelCreating` Methode.

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    Dieser Code weist die Entity Framework verwenden Sie gespeicherte Prozeduren für Einfüge-, update und delete-Operationen auf die `Department` Entität.
2. Geben Sie im Paketkonsole verwalten den folgenden Befehl aus:

    `add-migration DepartmentSP`

    Open *Migrationen\&Lt; Zeitstempel&gt;\_DepartmentSP.cs* , wie der Code in die `Up` Methode, die erstellt INSERT-, Update- und Delete gespeicherte Prozeduren:

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. Geben Sie im Paketkonsole verwalten den folgenden Befehl aus:

     `update-database`
4. Führen Sie die Anwendung im Debugmodus befindet, klicken Sie auf die **Abteilungen** Registerkarte, und klicken Sie dann auf **neu erstellen**.
5. Geben Sie Daten für eine neue Abteilung, und klicken Sie dann auf **erstellen**.

6. In Visual Studio sehen Sie sich in den Protokollen der **Ausgabe** Fenster zu sehen, dass eine gespeicherte Prozedur verwendet wurde, um die neue Abteilung Zeile einzufügen.

     ![Abteilung Insert SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Namen für Standardwerte, die gespeicherte Prozedur wird von Code First erstellt. Wenn Sie eine vorhandene Datenbank verwenden, müssen Sie die Namen der gespeicherten Prozedur anpassen, um bereits in der Datenbank definierten, gespeicherten Prozeduren zu verwenden. Informationen dazu, wie Sie dies tun, finden Sie unter [Entity Framework Code erste Insert/Update/Delete gespeicherte Prozeduren](https://msdn.microsoft.com/data/dn468673).

Wenn anpassen, welche gespeicherten Prozeduren werden generiert werden sollen, können Sie bearbeiten, dass das Codegerüst für Migrationen `Up` -Methode, die die gespeicherte Prozedur erstellt. Auf diese Weise werden die Änderungen jedes Mal, wenn wiedergegeben, dass die Migration wird ausgeführt und gelten für die Produktionsdatenbank bei Migrationen wird automatisch ausgeführt, in der Produktion nach der Bereitstellung.

Sollten Sie eine vorhandene gespeicherte Prozedur zu ändern, die in einer vorherigen Migration erstellt wurde, können Sie verwenden Sie den Befehl Add-Migration, um eine leere Migration zu generieren und dann manuell Schreiben von Code, der Aufrufe der [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) Methode .

## <a name="deploy-to-azure"></a>Bereitstellung in Azure

In diesem Abschnitt müssen Sie das optionale haben **Bereitstellen der app für Azure** im Abschnitt der [Migrationen und Bereitstellung](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) Tutorial dieser Reihe. Überspringen Sie diesen Abschnitt, wenn Sie Migrationen Fehler, die Sie enthält durch Löschen der Datenbank in Ihrem lokalen Projekt aufgelöst.

1. In Visual Studio mit der Maustaste des Projekts im **Projektmappen-Explorer** , und wählen Sie **veröffentlichen** aus dem Kontextmenü.
2. Klicken Sie auf **Veröffentlichen**.

    Visual Studio stellt die Anwendung in Azure bereit und öffnet die Anwendung in Ihrem Standardbrowser, die in Azure ausgeführt wird.
3. Testen Sie die Anwendung, überprüfen Sie, ob es funktioniert.

    Beim ersten einer Seite ausführen, auf die Datenbank zugreift, Entity Framework führt alle Migrationen `Up` Methoden erforderlich, um die Datenbank mit dem aktuellen Datenmodell auf dem neuesten Stand zu bringen. Sie können jetzt alle Webseiten verwenden, die Sie seit dem letzten, die Sie bereitgestellt haben hinzugefügt, einschließlich der Abteilung-Seiten, die Sie in diesem Lernprogramm hinzugefügt haben.

## <a name="get-the-code"></a>Abrufen des Codes

[Herunterladen des abgeschlossenen Projekts](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie in der [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren asynchronen code
> * Erstellt einen Controller für die Abteilung
> * Gespeicherte Prozeduren verwendet
> * In Azure bereitgestellt

Wechseln Sie zum nächsten Artikel erfahren Sie, wie Sie Konflikte behandeln, wenn mehrere Benutzer dieselbe Entität gleichzeitig aktualisieren.
> [!div class="nextstepaction"]
> [Behandeln von Parallelität](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
