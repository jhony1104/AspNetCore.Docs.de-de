---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Behandeln der Parallelität in EF in einer ASP.NET MVC 5-app'
description: In diesem Tutorial veranschaulicht das vollständigen Parallelität verwendet, um Konflikte zu behandeln, wenn mehrere Benutzer dieselbe Entität gleichzeitig aktualisieren.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 2b41b51de5fd6d87b9905ba2bb3ddf7cedb47e69
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889833"
---
# <a name="tutorial-handle-concurrency-with-ef-in-an-aspnet-mvc-5-app"></a>Tutorial: Behandeln der Parallelität in EF in einer ASP.NET MVC 5-app

In den vorherigen Tutorials haben Sie gelernt, wie Daten zu aktualisieren. In diesem Tutorial veranschaulicht das vollständigen Parallelität verwendet, um Konflikte zu behandeln, wenn mehrere Benutzer dieselbe Entität gleichzeitig aktualisieren. Sie ändern die Webseiten, die Arbeit mit der `Department` Entität, damit sie Parallelitätsfehler behandeln. In der nachfolgenden Abbildung sehen Sie die Seiten „Bearbeiten“ und „Löschen“, einschließlich einiger Meldungen, die angezeigt werden, wenn ein Parallelitätskonflikt auftritt.

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren Sie mehr über Parallelitätskonflikte
> * Hinzufügen der vollständigen Parallelität
> * Ändern des abteilungscontrollers
> * Test-Parallelitätsbehandlung
> * Aktualisieren der Seite „Delete“ (Löschen)

## <a name="prerequisites"></a>Vorraussetzungen

* [Asynchrone und gespeicherte Prozeduren](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="concurrency-conflicts"></a>Nebenläufigkeitskonflikte

Ein Parallelitätskonflikt tritt auf, wenn ein Benutzer die Daten einer Entität anzeigt, um diese zu bearbeiten, und ein anderer Benutzer eben diese Entitätsdaten aktualisiert, bevor die Änderungen des ersten Benutzers in die Datenbank geschrieben wurden. Wenn Sie die Erkennung solcher Konflikte nicht aktivieren, überschreibt das letzte Update der Datenbank die Änderungen des anderen Benutzers. In vielen Anwendungen ist dieses Risiko akzeptabel: Wenn es nur wenige Benutzer bzw. wenige Updates gibt, oder wenn es nicht schlimm ist, dass Änderungen überschrieben werden können, ist es den Aufwand, für die Parallelität zu programmieren, möglicherweise nicht wert. In diesem Fall müssen Sie für die Anwendung keine Behandlung von Nebenläufigkeitskonflikten konfigurieren.

### <a name="pessimistic-concurrency-locking"></a>Pessimistische Parallelität (Sperren)

Wenn Ihre Anwendung versehentliche Datenverluste in Parallelitätsszenarios verhindern muss, ist die Verwendung von Datenbanksperren eine Möglichkeit. Dies wird als bezeichnet *pessimistische Parallelität*. Bevor Sie zum Beispiel eine Zeile aus einer Datenbank lesen, fordern Sie eine Sperre für den schreibgeschützten Zugriff oder den Aktualisierungszugriff an. Wenn Sie eine Zeile für den Aktualisierungszugriff sperren, kann kein anderer Benutzer diese Zeile für den schreibgeschützten Zugriff oder den Aktualisierungszugriff sperren, da er eine Kopie der Daten erhalten würde, die gerade geändert werden. Wenn Sie eine Zeile für den schreibgeschützten Zugriff sperren, können andere diese Zeile ebenfalls für den schreibgeschützten Zugriff sperren, aber nicht für den Aktualisierungszugriff.

Das Verwalten von Sperren hat Nachteile. Es kann komplex sein, sie zu programmieren. Dies erfordert erhebliche Datenbankverwaltungsressourcen und kann mit steigender Anzahl der Benutzer einer Anwendung zu Leistungsproblemen führen. Aus diesen Gründen unterstützen nicht alle Datenbankverwaltungssysteme die pessimistische Parallelität. Bietet keine integrierte Unterstützung für das Entity Framework und in diesem Tutorial nicht angezeigt, wie er implementiert.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Die Alternative zur pessimistischen Parallelität ist *optimistische Parallelität*. Die Verwendung der optimistischen Parallelität bedeutet, Nebenläufigkeitskonflikte zu erlauben und entsprechend zu reagieren, wenn diese auftreten. Beispielsweise führt er die Seite Abteilungen zu bearbeiten, Änderungen der **Budget** Menge für die englische Abteilung von $350.000,00 in $0,00.

Bevor John klickt **speichern**, Andrea führt den gleichen Seite und ändert die **Startdatum** Feld 9/1/2007, 8/8/2013.

John klickt **speichern** zuerst und erkennt, die seine ändern, wenn der Browser die Indexseite, klicken Sie dann Jane gibt klickt **speichern**. Was daraufhin geschieht ist abhängig davon, wie Sie Nebenläufigkeitskonflikte handhaben. Einige der Optionen schließen Folgendes ein:

- Sie können nachverfolgen, welche Eigenschaft ein Benutzer geändert hat und nur die entsprechenden Spalten in der Datenbank aktualisieren. Im Beispielszenario würden keine Daten verloren gehen, da verschiedene Eigenschaften von zwei Benutzern aktualisiert wurden. Das nächste Mal eine Person den englischen Fachbereich durchsucht, die sie erhalten sowohl Johns und Jane die Änderungen, ein Datum von vor 8/8/2013 sowie ein Budget von 0 Dollar.

    Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlusten führen können. Sie kann Datenverluste jedoch nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft einer Entität vorgenommen werden. Ob Entity Framework auf diese Weise funktioniert, hängt davon ab, wie Sie Ihren Aktualisierungscode implementieren. Oft ist dies in Webanwendungen nicht praktikabel, da es erforderlich sein kann, viele Zustände zu verwalten, um alle ursprünglichen Eigenschaftswerte einer Entität und die neuen Werte im Auge zu behalten. Das Verwalten vieler Zuständen kann sich auf die Leistung der Anwendung auswirken, da es entweder Serverressourcen beansprucht oder in der Webseite selbst (z.B. in ausgeblendeten Feldern) oder in einem Cookie enthalten sein muss.
- Sie können die Janes Änderungen Benutzer2s die Änderungen zu überschreiben. Das nächste Mal eine Person den englischen Fachbereich durchsucht, sehen sie 8/8/2013 und der wiederhergestellte Wert $350.000,00. Das ist entweder ein *Client gewinnt*- oder ein *Letzter Schreiber gewinnt*-Szenario. (Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wie in der Einführung dieses Abschnitts beschrieben, geschieht dies automatisch, wenn Sie für die Behandlung der Parallelität keinen Code schreiben.
- Sie können verhindern, dass Janes Änderung in der Datenbank aktualisiert wird. In der Regel würden Sie eine Fehlermeldung angezeigt, zeigen sie den aktuellen Zustand der Daten und können ihr Zugriff auf ihre Änderungen erneut anzuwenden, wenn er immer noch machen möchte. Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt. (Die Werte des Datenspeichers haben Vorrang vor den Werten, die vom Client gesendet werden.) In diesem Tutorial implementieren Sie das Szenario „Speicher gewinnt“. Diese Methode stellt sicher, dass keine Änderung überschrieben wird, ohne dass ein Benutzer darüber benachrichtigt wird.

### <a name="detecting-concurrency-conflicts"></a>Erkennen von Nebenläufigkeitskonflikten

Sie können Konflikte auflösen, durch behandeln [OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) Ausnahmen, die vom Entity Framework ausgelöst. Entity Framework muss dazu in der Lage sein, Konflikte zu erkennen, damit es weiß, wann diese Ausnahmen ausgelöst werden sollen. Aus diesem Grund müssen Sie die Datenbank und das Datenmodell entsprechend konfigurieren. Einige der Optionen für das Aktivieren der Konflikterkennung schließen Folgendes ein:

- Fügen Sie eine Änderungsverfolgungsspalte in die Datenbanktabelle ein, die verwendet werden kann, um zu bestimmen, wenn eine Änderung an einer Zeile vorgenommen wurde. Anschließend können Sie konfigurieren die Entity Framework enthält diese Spalte in der `Where` -Klausel von SQL `Update` oder `Delete` Befehle.

    Der Datentyp der änderungsverfolgungsspalte ist in der Regel [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx). Die [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) Wert ist eine sequenzielle Zahl, die jeweils erhöht die Zeile aktualisiert. In einer `Update` oder `Delete` Befehl aus, die `Where` -Klausel enthält, den ursprünglichen Wert der änderungsverfolgungsspalte (die ursprüngliche Zeilenversion). Wenn von einem anderen Benutzer, der Wert in der zu aktualisierende Zeile geändert wurde der `rowversion` Spalte unterscheidet sich von den ursprünglichen Wert daher `Update` oder `Delete` Anweisung wurde nicht gefunden zu aufgrund von zu aktualisierenden Zeile die `Where` Klausel. Wenn Entity Framework feststellt, wurden keine Zeilen von aktualisiert, die `Update` oder `Delete` Befehl (d. h., wenn die Anzahl der betroffenen Zeilen 0 (null) ist), die als Parallelitätskonflikt interpretiert.
- Konfigurieren von Entity Framework für die ursprünglichen Werte jeder Spalte in der Tabelle sind die `Where` -Klausel der `Update` und `Delete` Befehle.

    Wie in der ersten Option, wenn etwas in der Zeile geändert hat, da die Zeile zuerst gelesen wurde die `Where` Klausel keine Zeile zum Aktualisieren, das Entity Framework als Parallelitätskonflikt interpretiert zurück. Für Datenbanktabellen, die viele Spalten aufweisen. dieser Ansatz kann dazu führen, sehr große `Where` -Klauseln, und Sie können festlegen, dass Sie mit große zustandsdatenmengen verwalten. Wie bereits erwähnt, kann das Verwalten großer Mengen von Zuständen die Anwendungsleistung beeinträchtigen. Deshalb wird dieser Ansatz in der Regel nicht empfohlen, und ist nicht die Methode, die in diesem Tutorial verwendet wird.

    Wenn Sie diesen Ansatz für die Parallelität implementieren möchten, müssen Sie alle nicht-primären Schlüsseleigenschaften in der Entität markieren Sie Parallelität für durch Hinzufügen von nachverfolgen möchten die [ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx) -Attributs. Dass die Änderung der Entity Framework enthalten alle Spalten in der SQL ermöglicht `WHERE` -Klausel der `UPDATE` Anweisungen.

Im weiteren Verlauf dieses Tutorials fügen Sie eine [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) tracking-Eigenschaft, um die `Department` Entität, einen Controller und Ansichten zu erstellen und testen, um sicherzustellen, dass alles ordnungsgemäß funktioniert.

## <a name="add-optimistic-concurrency"></a>Hinzufügen der vollständigen Parallelität

In *Models\Department.cs*, Hinzufügen einer Nachverfolgungseigenschaft namens `RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

Die [Zeitstempel](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) Attribut gibt an, dass in dieser Spalte berücksichtigt werden die `Where` -Klausel der `Update` und `Delete` Befehle, die an die Datenbank gesendet. Das Attribut heißt [Zeitstempel](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) da frühere Versionen von SQL Server eine SQL verwendet [Zeitstempel](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx) -Datentyp, bevor SQL [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) ersetzt. Der Typ .net für [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) ist ein Bytearray.

Wenn Sie die fluent-API verwenden möchten, können Sie mithilfe der [IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx) Methode, um die änderungsverfolgungseigenschaft anzugeben, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Durch das Hinzufügen einer Eigenschaft ändern Sie das Datenbankmodell, daher müssen Sie eine weitere Migration durchführen. Geben Sie die folgenden Befehle in die Paket-Manager-Konsole ein:

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-department-controller"></a>Ändern des abteilungscontrollers

In *Controllers\DepartmentController.cs*, Hinzufügen einer `using` Anweisung:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

In der *DepartmentController.cs* Datei, ändern Sie alle vier Vorkommen von "LastName" in "FullName", sodass die Dropdownlisten Administrator einer Abteilung der vollständige Name des Dozenten und nicht nur den Nachnamen enthalten soll.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

Ersetzen Sie den Code für die `HttpPost` `Edit` Methode durch den folgenden Code:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Wenn die Methode `FindAsync` NULL zurückgibt, wurde die Abteilung von einem anderen Benutzer gelöscht. Der Code verwendet die bereitgestellten Formularwerte zum Erstellen einer abteilungsentität, damit die Seite "Bearbeiten" mit einer Fehlermeldung erneut angezeigt werden kann. Alternativ müssen Sie die Abteilungsentität nicht erneut erstellen, wenn Sie nur eine Fehlermeldung anzeigen, ohne die Abteilungsfelder erneut anzuzeigen.

Die Ansicht speichert den ursprünglichen `RowVersion` Wert in einem ausgeblendeten Feld, und die Methode erhält in der `rowVersion` Parameter. Bevor Sie `SaveChanges` aufrufen, müssen Sie diesen ursprünglichen Eigenschaftswert von `RowVersion` in die Auflistung `OriginalValues` für die Entität einfügen. Klicken Sie dann, wenn Entity Framework SQL erstellt `UPDATE` Befehl ",", mit dem Befehl enthält eine `WHERE` -Klausel, die für eine Zeile sucht mit dem ursprünglichen `RowVersion` Wert.

Wenn keine Zeilen betroffen sind der `UPDATE` Befehl (keine Zeile enthält die ursprünglichen `RowVersion` Wert), löst Entity Framework eine `DbUpdateConcurrencyException` Ausnahme und der Code in die `catch` Block Ruft die betroffene `Department` Entität aus der Ausnahme -Objekt.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Dieses Objekt verfügt über die neuen Werte eingegeben haben, vom Benutzer in seiner `Entity` -Eigenschaft, und Sie erhalten die Werte aus der Datenbank gelesen werden, durch den Aufruf der `GetDatabaseValues` Methode.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Die `GetDatabaseValues` Methodenrückgabe null, wenn ein Benutzer die Zeile aus der Datenbank gelöscht wurde; andernfalls, müssen Sie das zurückgegebene Objekt zum Umwandeln der `Department` Klasse, um Zugriff auf die `Department` Eigenschaften. (Da Sie bereits zum Löschen überprüft `databaseEntry` wäre null nur dann, wenn die Abteilung, nach dem gelöscht wurde `FindAsync` ausgeführt wird und vor dem `SaveChanges` ausgeführt wird.)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Als Nächstes fügt der Code eine benutzerdefinierte Fehlermeldung für jede Spalte, die Datenbankwerte sich von der Eingabe des Benutzers auf der Seite "Bearbeiten" hinzu:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Eine längere Fehlermeldung wird erläutert, was passiert ist und was Sie tun:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Mit dem Code Abschließend wird die `RowVersion` Wert, der die `Department` Objekt, das den neuen Wert aus der Datenbank abgerufen. Dieser neue `RowVersion`-Wert wird in dem ausgeblendeten Feld gespeichert, wenn die Seite „Bearbeiten“ erneut angezeigt wird. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die nach dem erneuten Anzeigen der Seite „Bearbeiten“ aufgetreten sind.

In *Views\Department\Edit.cshtml*, fügen Sie ein ausgeblendetes Feld zum Speichern der `RowVersion` Eigenschaftswert, sofort nach dem ausgeblendeten Feld für die `DepartmentID` Eigenschaft:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="test-concurrency-handling"></a>Test-Parallelitätsbehandlung

Führen Sie die Website, und klicken Sie auf **Abteilungen**.

Klicken Sie mit der rechten Maustaste auf die **bearbeiten** Link für den englischen Fachbereich, und wählen **in neuer Registerkarte öffnen** klicken Sie dann auf die **bearbeiten** Link für die englische Abteilung. Die beiden Registerkarten werden dieselben Informationen angezeigt.

Ändern Sie ein Feld in der ersten Registerkarte, und klicken Sie auf **Speichern**.

Der Browser zeigt die Indexseite mit dem geänderten Wert an.

Ändern Sie ein Feld in der zweiten Registerkarte, und klicken Sie auf **speichern**. Folgende Fehlermeldung wird angezeigt:

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

Klicken Sie erneut auf **Speichern**. Der Wert, den Sie in der zweiten Registerkarte eingegeben haben, wird zusammen mit den ursprünglichen Wert der Daten gespeichert, die Sie in der ersten Browser geändert haben. Die gespeicherten Werte werden Ihnen auf der Indexseite angezeigt.

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Bei der Seite „Löschen“ entdeckt Entity Framework Nebenläufigkeitskonflikte, die durch die Bearbeitung einer Abteilung ausgelöst wurden, auf ähnliche Weise. Wenn die `HttpGet` `Delete` Methode bestätigungsansicht anzeigt, die Ansicht enthält die ursprüngliche `RowVersion` Wert in einem verborgenen Feld. Dieser Wert dann zur Verfügung, um wird die `HttpPost` `Delete` -Methode, die aufgerufen wird, wenn der Benutzer das Löschen bestätigt. Wenn Entity Framework erstellt SQL `DELETE` Befehl, er enthält eine `WHERE` Klausel mit dem ursprünglichen `RowVersion` Wert. Wenn die Ergebnisse des Befehls in 0 (null) Zeilen betroffen (d. h. die Zeile wurde geändert, nachdem die Bestätigungsseite "löschen" angezeigt wurde), wird eine Parallelitätsausnahme ausgelöst, und die `HttpGet Delete` Methode wird aufgerufen, mit dem Fehler Kennzeichen festgelegt `true` um Kostenoptionen der Bestätigungsseite mit einer Fehlermeldung. Es ist auch möglich, dass keine Zeilen betroffen sind, da die Zeile von einem anderen Benutzer gelöscht wurde, sodass in diesem Fall eine andere Fehlermeldung angezeigt wird.

In *DepartmentController.cs*, ersetzen Sie die `HttpGet` `Delete` Methode durch den folgenden Code:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Die Methode akzeptiert einen optionalen Parameter, der angibt, ob die Seite nach einem Parallelitätsfehler erneut angezeigt wird. Wenn dieses Flag ist `true`, eine Fehlermeldung wird gesendet, um die Ansicht mit einem `ViewBag` Eigenschaft.

Ersetzen Sie den Code in die `HttpPost` `Delete` Methode (mit dem Namen `DeleteConfirmed`) durch den folgenden Code:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

In dem eingerüsteten Code, den Sie soeben ersetzt haben, akzeptiert diese Methode nur eine Datensatz-ID:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Du hast geändert, diesen Parameter, um eine `Department` Entitätsinstanz, die von der modellbindung erstellt wurde. So erhalten Sie Zugriff auf die `RowVersion` neben dem Datensatzschlüssel auch-Eigenschaftswert.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Ebenfalls haben Sie den Namen der Aktionsmethode `DeleteConfirmed` auf `Delete` geändert. Der eingerüstete Code, der mit dem Namen der `HttpPost` `Delete` Methode `DeleteConfirmed` gerne die `HttpPost` Methode eine eindeutige Signatur. (Die CLR erfordert überladene Methoden, um verschiedene Methodenparameter zu haben.) Nun, dass die Signaturen eindeutig sind, können Sie mit der MVC-Konvention halten und den gleichen Namen für die `HttpPost` und `HttpGet` delete-Methoden.

Wenn ein Parallelitätsfehler abgefangen wird, zeigt der Code erneut die Bestätigungsseite „Löschen“ an, und stellt ein Flag bereit, das angibt, dass eine Fehlermeldung für die Parallelität angezeigt werden soll.

In *Views\Department\Delete.cshtml*, ersetzen Sie den eingerüsteten Code durch den folgenden Code, der ein Nachrichtenfeld Fehlermeldung und ausgeblendete Felder für die Eigenschaften "DepartmentID" und RowVersion-Werte hinzufügt. Die Änderungen werden hervorgehoben.

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

Dieser Code Fügt eine Fehlermeldung zwischen der `h2` und `h3` Überschriften:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Es ersetzt `LastName` mit `FullName` in die `Administrator` Feld:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Schließlich fügt sie ausgeblendete Felder für die `DepartmentID` und `RowVersion` Eigenschaften nach der `Html.BeginForm` Anweisung:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

Führen Sie die Indexseite "Abteilungen". Klicken Sie mit der rechten Maustaste auf die **löschen** Link für den englischen Fachbereich, und wählen **in neuer Registerkarte öffnen** klicken Sie dann in der ersten Registerkarte auf die **bearbeiten** Link für die englische Abteilung.

Im ersten Fenster einen der Werte ändern, und klicken Sie auf **speichern**.

Die Indexseite wird die Änderung bestätigt.

Klicken Sie in der zweiten Registerkarte auf **Löschen**.

Ihnen wird eine Fehlermeldung zur Parallelität angezeigt, und die Abteilungswerte werden mit den aktuellen Werten der Datenbank aktualisiert.

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Wenn Sie erneut auf **Löschen** klicken, werden Sie auf die Indexseite weitergeleitet, die anzeigt, dass die Abteilung gelöscht wurde.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie in der [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

Weitere Informationen über andere Möglichkeiten, verschiedene Szenarien zu behandeln, finden Sie unter [optimistische Parallelitätsmuster](https://msdn.microsoft.com/data/jj592904) und [arbeiten mit Eigenschaftswerten](https://msdn.microsoft.com/data/jj592677) auf MSDN. Das nächste Tutorial zeigt, wie Sie Tabelle pro Hierarchie-Vererbung für implementieren die `Instructor` und `Student` Entitäten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Informationen über Parallelitätskonflikte
> * Hinzugefügte optimistische Parallelität
> * Geänderte abteilungscontrollers
> * Getestete Parallelitätsbehandlung
> * Aktualisiert die Seite "löschen"

Wechseln Sie zum nächsten Artikel erfahren, wie Sie Vererbung in das Datenmodell zu implementieren.
> [!div class="nextstepaction"]
> [Implementieren der Vererbung im Datenmodell](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)