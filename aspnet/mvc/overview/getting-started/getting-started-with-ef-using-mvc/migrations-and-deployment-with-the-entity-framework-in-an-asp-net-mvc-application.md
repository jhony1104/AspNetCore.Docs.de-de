---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Verwenden von EF-Migrationen in einer ASP.NET MVC-app und Bereitstellen in Azure'
author: tdykstra
description: In diesem Tutorial haben Sie Code First-Migrationen zu aktivieren und Bereitstellen der Anwendung in der Cloud in Azure.
ms.author: riande
ms.date: 01/16/2019
ms.topic: tutorial
ms.assetid: d4dfc435-bda6-4621-9762-9ba270f8de4e
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 131540113f5ac8ce9e15c8ab92b8dc7ee11de115
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444219"
---
# <a name="tutorial-use-ef-migrations-in-an-aspnet-mvc-app-and-deploy-to-azure"></a>Tutorial: Verwenden von EF-Migrationen in einer ASP.NET MVC-app und Bereitstellen in Azure

Bisher wurde die Contoso University-Beispielwebanwendung lokal in IIS Express auf Ihrem Entwicklungscomputer ausgeführt wurde. Um eine reale Anwendung für andere Benutzer über das Internet verfügbar zu machen, müssen Sie es auf einen Webhostinganbieter bereitstellen. In diesem Tutorial haben Sie Code First-Migrationen zu aktivieren und Bereitstellen der Anwendung in der Cloud in Azure:

- Aktivieren Sie Code First-Migrationen. Das Migrationsfeature können Sie das Datenmodell ändern und die Änderungen für die Produktion bereitstellen, indem Sie das Datenbankschema ohne löschen und Neuerstellen der Datenbank aktualisieren.
- In Azure bereitgestellt. Dieser Schritt ist optional. Sie können mit den restlichen Tutorials fortfahren, ohne dass das Projekt bereitgestellt.

Es wird empfohlen, dass Sie eine continuous Integration-Prozesses mit der quellcodeverwaltung für die Bereitstellung verwenden, aber diese Themen in diesem Tutorial nicht behandelt. Weitere Informationen finden Sie unter den [quellcodeverwaltung](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) und [fortlaufende Integration](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) Kapiteln [Building Real-World Cloud-Apps mit Azure](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

In diesem Tutorial:

> [!div class="checklist"]
> * Aktivieren Sie Code First-Migrationen
> * Bereitstellen der app in Azure (optional)

## <a name="prerequisites"></a>Vorraussetzungen

- [Verbindungsresilienz und Abfangen von Befehlen](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-code-first-migrations"></a>Aktivieren Sie Code First-Migrationen

Wenn Sie eine neue Anwendung entwickeln, ändert sich Ihr Datenmodell häufig. Jedes Mal, wenn das Datenmodell geändert wird, ist es nicht mehr synchron mit der Datenbank. Sie haben konfiguriert, dass das Entity Framework für das automatische Löschen und Neuerstellen der Datenbank jedes Mal, die Sie das Datenmodell ändern. Wenn Sie hinzufügen, entfernen oder Ändern von Entitätsklassen oder Ändern Ihrer `DbContext` Klasse, die beim nächsten der Anwendung ausführen es automatisch die vorhandene Datenbank gelöscht wird, erstellt eine neue Ressourcengruppe, die dem Modell entspricht, und startet diese mit Testdaten.

Diese Methode, bei der die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die Anwendung für die Produktion bereitstellen. Wenn die Anwendung in der Produktion ausgeführt wird, wird es in der Regel Speichern von Daten, die Sie beibehalten möchten, und nicht alles, was jedes Mal verloren gehen, die Sie z. B. das Hinzufügen einer neuen Spalteninhalts ändern möchten. Die [Code First-Migrationen](https://msdn.microsoft.com/data/jj591621) löst dieses Problem durch Code First zum Aktualisieren des Datenbankschemas löschen und Neuerstellen der Datenbank aktivieren. In diesem Tutorial stellen Sie die Anwendung bereit, und aktivieren, die Sie die Vorbereitung Migrationen.

1. Deaktivieren Sie den Initialisierer, die Sie zuvor eingerichtet haben durch auskommentieren oder Löschen der `contexts` -Element, das Sie die Datei Web.config der Anwendung hinzugefügt.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.xml?highlight=2,6)]
2. Auch in der Anwendung *"Web.config"* Datei, ändern Sie den Namen der Datenbank in der Verbindungszeichenfolge in "contosouniversity2".

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.xml?highlight=2)]

    Diese Änderung wird das Projekt eingerichtet, sodass die erste Migration eine neue Datenbank erstellt. Dies ist nicht erforderlich, aber Sie werden später sehen, warum es eine gute Idee ist.
3. Öffnen Sie das Menü **Extras**, und wählen Sie **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

1. Auf der `PM>` Eingabeaufforderung Geben Sie die folgenden Befehle aus:

    ```text
    enable-migrations
    add-migration InitialCreate
    ```

    Die `enable-migrations` Befehl erstellt eine *Migrationen* Ordner im Projekt ContosoUniversity, und es wird in diesem Ordner eine *Configuration.cs* -Datei, die Sie bearbeiten können, um die Migrationen zu konfigurieren.

    (Falls Sie die oben genannten Schritt, die Sie zum Ändern des Datenbanknamens weiterleitet übersehen, Migrationen findet die vorhandene Datenbank und automatisch die `add-migration` Befehl. Das ist in Ordnung, es bedeutet lediglich, dass Sie einen Test des Codes Migrationen ausführen wird nicht, bevor Sie die Datenbank bereitstellen. Später beim Ausführen der `update-database` Befehls geschieht nichts, da die Datenbank bereits vorhanden ist.)

    Öffnen der *ContosoUniversity\Migrations\Configuration.cs* Datei. Initialisiererklasse, die Sie schon kennen wie die `Configuration` Klasse enthält eine `Seed` Methode.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

    Der Zweck der [Ausgangswert](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) Methode besteht darin, ermöglichen es Ihnen, einfügen oder aktualisieren Testdaten aus, nach dem Code First erstellt oder die Datenbank aktualisiert. Die Methode wird aufgerufen, wenn die Datenbank erstellt wird, und jedes Mal, wenn das Datenbankschema aktualisiert wird, nachdem ein Datenmodell ändern.

### <a name="set-up-the-seed-method"></a>Richten Sie die Seed-Methode

Wenn Sie löschen und die Datenbank für jede datenmodelländerung Neuerstellen, Sie verwenden die Initialisiererklasse `Seed` Methode, um Testdaten einzufügen, da nach jeder modelländerung die Datenbank gelöscht wird und die Testdaten geht verloren. Code First-Migrationen, Test, der Daten, nachdem Änderungen in der Datenbank beibehalten werden, also mit den Testdaten in die [Ausgangswert](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) Methode ist in der Regel nicht erforderlich. In der Tat nicht sollen die `Seed` Methode, um Testdaten einzufügen, wenn Sie Migrationen zum Bereitstellen der Datenbank für die Produktion, verwenden da die `Seed` Methode in der Produktion ausgeführt. In diesem Fall soll die `Seed` -Methode in der Datenbank nur die Daten in der Produktion. Sie können z. B. die Datenbank im tatsächlichen Abteilungsnamen enthalten die `Department` Tabelle, wenn die Anwendung in der Produktion verfügbar ist.

In diesem Tutorial verwenden Sie Migrationen für die Bereitstellung, aber das `Seed` Methode fügt Testdaten ohnehin um leichter sehen, wie die Funktionalität der Anwendung funktioniert, ohne eine große Datenmenge manuell einfügen.

1. Ersetzen Sie die *Configuration.cs* Datei durch den folgenden Code, der Daten in die neue Datenbank lädt.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    Die [Ausgangswert](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) Methode nimmt das Datenbank-Context-Objekt als Eingabeparameter und der Code in der Methode verwendet das Objekt auf der Datenbank neue Entitäten hinzugefügt. Für jeden Entitätstyp, der Code erstellt eine Auflistung von neuen Entitäten, fügt diese an die entsprechende ["DbSet"](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx) Eigenschaft, und klicken Sie dann speichert die Änderungen an der Datenbank. Es ist nicht notwendig, die ["SaveChanges"](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) Methode nach jeder Gruppe von Entitäten, wie hier geschehen ist, aber dies hilft Ihnen, die Ursache eines Problems zu suchen, wenn eine Ausnahme auftritt, während der Code in die Datenbank geschrieben werden.

    Verwenden Sie einige der Anweisungen, die Daten einfügen der [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) Methode zum Ausführen eines Vorgangs "Upsert". Da die `Seed` Methode wird bei jedem Ausführen der `update-database` Befehl nach der jede Migration in der Regel Daten, können nicht nur eingefügt werden, da die Zeilen, die Sie hinzufügen möchten es nach der ersten Migration sein werden, der die Datenbank erstellt. Der Vorgang "Upsert" wird verhindert, dass Fehler, die passieren würde, wenn Sie versuchen, fügen Sie eine Zeile, die bereits vorhanden ist, aber es ***überschreibt*** Änderungen an Daten, die Sie möglicherweise beim Testen der Anwendung vorgenommen haben. Mit Testdaten in einigen Tabellen möchten Sie vielleicht nicht so: in einigen Fällen, wenn Sie Daten während des Tests ändern, sollen die Änderungen, um nach Datenbankupdates bleiben. In diesem Fall einen bedingte Insert-Vorgang ausgeführt werden soll: Fügen Sie eine Zeile nur dann, wenn er nicht bereits vorhanden. Die Seed-Methode verwendet beide Ansätze.

    Der erste Parameter zu übergeben, um die [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) Methode gibt die Eigenschaft zu verwenden, um zu überprüfen, ob eine Zeile bereits vorhanden ist. Für die Testdaten für "Student", die Sie bereitstellen, die `LastName` Eigenschaft kann für diesen Zweck verwendet werden, da jedes Nachnamens in der Liste eindeutig ist:

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Dieser Code wird davon ausgegangen, dass die Nachnamen eindeutig sind. Wenn ein Kursteilnehmer mit doppelten Nachnamen manuell erhalten Sie die folgende Ausnahme beim nächsten eine Migration durchzuführen:

    **Die Sequenz enthält mehr als ein element**

    Weitere Informationen zur Behandlung von redundanter Daten wie z. B. zwei Schüler/Studenten, die mit dem Namen "Alexander Carson" finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) andersons Blog. Weitere Informationen zu den `AddOrUpdate` -Methode finden Sie unter [kümmern mit EF 4.3 AddOrUpdate Methode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) Julie lermans Blog.

    Der Code, der erstellt `Enrollment` Entitäten wird vorausgesetzt, dass die `ID` Wert in den Entitäten der `students` -Auflistung, obwohl Sie diese Eigenschaft im Code festgelegt haben, die die Auflistung erstellt.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=2)]

    Können Sie die `ID` hier Eigenschaft da die `ID` Wert wird festgelegt, wenn Sie aufrufen `SaveChanges` für die `students` Auflistung. EF ruft automatisch der primäre Schlüsselwert ab, wenn eine Entität in die Datenbank eingefügt und aktualisiert die `ID` Eigenschaft der Entität im Speicher.

    Der Code, der den hinzugefügt `Enrollment` Entität, die die `Enrollments` Entitätenmenge verwendet nicht die `AddOrUpdate` Methode. Es überprüft, ob eine Entität bereits vorhanden ist und die Entität eingefügt, wenn er nicht vorhanden ist. Dieser Ansatz wird vorgenommene Änderungen beizubehalten, die Sie an einer Registrierung auf Unternehmensniveau mit der Benutzeroberfläche der Anwendung. Der Code durchläuft jedes Element der `Enrollment` [Liste](https://msdn.microsoft.com/library/6sh2ey19.aspx) und wenn die Registrierung in der Datenbank nicht gefunden wird, wird die Registrierung mit der Datenbank hinzugefügt. Beim ersten der Datenbank Aktualisierung, wird die Datenbank leer sein, sodass jede Registrierung hinzugefügt werden.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

2. Erstellen Sie das Projekt.

### <a name="execute-the-first-migration"></a>Führen Sie die erste migration

Beim Ausführen der `add-migration` Befehl Migrationen generiert den Code, der die Datenbank von Grund auf neu erstellen würden. Dieser Code befindet sich auch in der *Migrationen* Ordner, in der Datei, die mit dem Namen  *&lt;Zeitstempel&gt;\_InitialCreate.cs*. Die `Up` Methode der `InitialCreate` Klasse erstellt, die Datenbanktabellen, die den datenmodellentitätenmengen entsprechen, und die `Down` Methode löscht sie.

[!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren. Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.

Dies ist die ursprüngliche Migration erstellt wurde, als Sie eingegeben haben, die die `add-migration InitialCreate` Befehl. Der Parameter (`InitialCreate` im Beispiel) wird verwendet, für die Datei benennen und kann beliebig, die Sie in der Regel wählen Sie ein Wort oder Ausdruck, der zusammengefasst, was bei der Migration erfolgt. Sie können z. B. eine spätere Migration nennen &quot;AddDepartmentTable&quot;.

Wenn Sie die ursprüngliche Migration erstellt haben, als die Datenbank bereits vorhanden war, wird der Code für die Datenbankerstellung zwar generiert, allerdings muss er nicht ausgeführt werden, da die Datenbank bereits dem Datenmodell entspricht. Wenn Sie die App in einer anderen Umgebung bereitstellen, in der die Datenbank noch nicht vorhanden ist, wird dieser Code ausgeführt, um Ihre Datenbank zu erstellen. Daher sollte dieser zunächst getestet werden. Deshalb Sie geändert haben, den Namen der Datenbank in der Verbindungszeichenfolge zuvor&mdash;, damit Migrationen eine von Grund auf neu erstellen können.

1. In der **-Paket-Manager-Konsole** Fenster, geben Sie den folgenden Befehl aus:

    `update-database`

    Die `update-database` Befehl führt die `Up` Methode zum Erstellen der Datenbank aus, und klicken Sie dann es ausgeführt wird die `Seed` Methode, um die Datenbank zu füllen. Der gleiche Prozess wird automatisch dann in der Produktion ausgeführt, nach der Bereitstellung der Anwendung, wie Sie im folgenden Abschnitt sehen werden.
2. Verwendung **Server-Explorer** auf die Datenbank zu untersuchen, wie im ersten Tutorial aus, und führen Sie die Anwendung aus, um sicherzustellen, dass alles weiterhin wie zuvor gleichermaßen funktioniert.

## <a name="deploy-to-azure"></a>Bereitstellung in Azure

Bisher wurde die Anwendung lokal in IIS Express auf Ihrem Entwicklungscomputer ausgeführt wurde. Um es für andere Benutzer über das Internet verfügbar zu machen, müssen Sie es auf einen Webhostinganbieter bereitstellen. In diesem Abschnitt des Tutorials stellen Sie es in Azure bereit. In diesem Abschnitt ist optional. Sie können diesen Schritt überspringen, und fahren Sie mit dem folgenden Tutorial fort, oder können Sie die Anweisungen in diesem Abschnitt für einen anderen Hostinganbieter Ihrer Wahl anpassen.

### <a name="use-code-first-migrations-to-deploy-the-database"></a>Verwenden von Code First-Migrationen zum Bereitstellen der Datenbank

Um die Datenbank bereitzustellen, verwenden Sie Code First-Migrationen. Wenn Sie das Veröffentlichungsprofil, die Sie zum Konfigurieren von Einstellungen erstellen für die Bereitstellung aus Visual Studio verwenden, wählen Sie das Kontrollkästchen mit der Bezeichnung **Update Database**. Diese Einstellung bewirkt, dass den Bereitstellungsprozess so konfigurieren Sie die Anwendung automatisch *"Web.config"* Datei auf dem Zielserver so, dass Code First verwendet die `MigrateDatabaseToLatestVersion` datenbankinitialisiererklasse.

Visual Studio nichts mit der Datenbank während des Bereitstellungsprozesses, während sie Ihr Projekt auf den Zielserver kopiert. Wenn Sie die bereitgestellte Anwendung ausführen, und greift auf die Datenbank zum ersten Mal nach der Bereitstellung, überprüft Code First, wenn die Datenbank mit dem Datenmodell übereinstimmt. Wenn ein Konflikt vorliegt, Code First erstellt automatisch die Datenbank (sofern noch keiner vorhanden ist) oder das Datenbankschema auf die neueste Version aktualisiert wird, (Wenn eine Datenbank vorhanden ist, aber es nicht das Modell entspricht). Wenn die Anwendung eine Migrationen implementiert `Seed` -Methode, die Methode ausgeführt wird, nachdem die Datenbank wird erstellt, oder das Schema wird aktualisiert.

Ihre Migrationen `Seed` -Methode fügt die Testdaten. Wenn Sie in einer produktionsumgebung bereitstellen, müssten Sie ändern die `Seed` Methode, sodass die It nur Daten einfügt, die in der-Datenbank eingefügt werden sollen. Z. B. in Ihrem aktuellen Datenmodell empfiehlt um echte Kurse, aber fiktive Schüler/Studenten in der Entwicklungsdatenbank zu erhalten. Können Sie schreiben eine `Seed` Methode, um sowohl in der Entwicklung zu laden und kommentieren Sie die fiktiven Schüler/Studenten, bevor Sie in der produktionsumgebung bereitstellen. Oder Sie schreiben eine `Seed` Methode, um nur Kurse zu laden, und geben die fiktiven Schüler/Studenten in der Testdatenbank manuell mithilfe der Benutzeroberfläche der Anwendung.

### <a name="get-an-azure-account"></a>Ein Azure-Konto erhalten

Sie benötigen ein Azure-Konto. Wenn diese nicht bereits vorhanden, aber Sie Visual Studio-Abonnement müssen, können Sie [aktivieren Sie die Vorteile der Subscription](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/
). Andernfalls können Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [kostenlose Azure-Testversion](https://azure.microsoft.com/free/).

### <a name="create-a-web-site-and-a-sql-database-in-azure"></a>Erstellen Sie eine Website und einer SQL-Datenbank in Azure

Ihre Web-app in Azure wird in einer freigegebenen Hostingumgebung ausgeführt, was bedeutet, dass sie auf virtuellen Computern (VMs) ausgeführt wird, die gemeinsam mit anderen Azure-Clients verwendet werden. Eine freigegebene Hostingumgebung ist eine kostengünstige Möglichkeit für den Einstieg in die Cloud. Wenn der Webdatenverkehr zunimmt, kann später die Anwendung skalieren, um die Anforderungen zu erfüllen, indem Sie auf dedizierten virtuellen Computern ausführen. Weitere Informationen zu den Preisoptionen für Azure App Service finden unter [App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/).

Sie stellen die Datenbank in Azure SQL-Datenbank bereit. SQL-Datenbank ist eine cloudbasierte relationale Datenbank-Dienst, der auf SQL Server-Technologien basiert. Tools und Anwendungen, die Arbeit mit SQL Server funktionieren auch mit SQL-Datenbank.

1. In der [Azure-Verwaltungsportal](https://portal.azure.com), wählen Sie **erstellen Sie eine Ressource** in der linken Registerkarte, und wählen Sie dann **alle** auf die **neu** Bereich (oder *Blatt*) auf alle verfügbaren Ressourcen finden Sie unter. Wählen Sie **Web-App + SQL** in die **Web** Teil der **alles** auf dem Blatt. Wählen Sie abschließend **erstellen**.

    ![Erstellen Sie eine Ressource im Azure-portal](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/create-azure-resource.png)

   Das Formular zum Erstellen eines neuen **neue Web-App + SQL** Ressource wird geöffnet.

2. Geben Sie eine Zeichenfolge in die **Anwendungsnamen** Feld als eindeutige URL für Ihre Anwendung verwenden. Die vollständige URL besteht aus der hier sowie die Standarddomäne von Azure App Services eingegebenen (. azurewebsites.net). Wenn die **Anwendungsnamen** bereits erstellt ist, wird der Assistent benachrichtigt Sie mit einem roten *der app-Name ist nicht verfügbar* Nachricht. Wenn die **Anwendungsnamen** ist verfügbar, Sie ein grünes Häkchen angezeigt.

3. In der **Abonnement** wählen Sie das Azure-Abonnement, in denen Sie möchten, die **App Service** befinden.

4. In der **Ressourcengruppe** Textfeld, wählen Sie eine Ressourcengruppe oder ein neues erstellen. Diese Einstellung gibt an, welches Datencenter Ihrer Website ausgeführt wird. Weitere Informationen zu Ressourcengruppen finden Sie unter [Ressourcengruppen](/azure/azure-resource-manager/resource-group-overview#resource-groups).

5. Erstellen Sie ein neues **App Service-Plan** durch Klicken auf die *App Service-Abschnitt*, **neu erstellen**, und geben Sie **App Service-Plan** (kann denselben Namen wie sein App Service), **Speicherort**, und **Tarif** (besteht eine Option "free").

6. Klicken Sie auf **SQL-Datenbank**, und wählen Sie dann **Erstellen einer neuen Datenbank** oder eine vorhandene Datenbank auswählen.

7. In der **Namen** Geben Sie einen Namen für Ihre Datenbank.
8. Klicken Sie auf die **Zielserver** Feld, und wählen Sie dann **Erstellen eines neuen Servers**. Wenn Sie einen Server zuvor erstellt haben, können Sie auch diesen Server aus der Liste der verfügbaren Server auswählen.
9. Wählen Sie **Tarif** Abschnitt *Free*. Wenn zusätzliche Ressourcen erforderlich sind, kann die Datenbank bis zu einem beliebigen Zeitpunkt hochskaliert werden. Informationen zu Azure SQL-Preisen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/managed/).
10. Ändern Sie [Sortierreihenfolge](/sql/relational-databases/collations/collation-and-unicode-support) je nach Bedarf.
11. Geben Sie einen Administrator **SQL-Administratorname** und **SQL-Administratorkennwort**.

   - Wenn Sie ausgewählt haben **neue SQL-Datenbankserver**, definieren Sie einen neuen Namen und das Kennwort, das Sie später verwenden werden, wenn Sie die Datenbank zugreifen.
   - Wenn Sie einen Server, den Sie zuvor erstellt haben ausgewählt, geben Sie Anmeldeinformationen für diesen Server.

12. Erfassung von Telemetriedaten kann für App Service mithilfe von Application Insights aktiviert werden. Mit wenig Konfiguration erfasst Application Insights wertvolle-Ereignis, Ausnahme, Abhängigkeit, Anforderung und Ablaufverfolgungsinformationen. Weitere Informationen zu Application Insights finden Sie unter [Azure Monitor](https://azure.microsoft.com/services/monitor/).
13. Klicken Sie auf **erstellen** unten, um anzugeben, dass Sie fertig sind.

    Das Verwaltungsportal gibt zurück, auf der Dashboard-Seite, und die **Benachrichtigungen** Bereich am oberen Rand der Seite zeigt, dass die Website erstellt wird. Nach einer Weile (in der Regel weniger als einer Minute) wird eine Benachrichtigung, die die Bereitstellung erfolgreich war. In der Navigationsleiste auf der linken Seite der neuen App Service wird angezeigt, der **Anwendungsdienste** Abschnitt und die neue SQL-Datenbank wird in der **SQL-Datenbanken** Abschnitt.

### <a name="deploy-the-app-to-azure"></a>Bereitstellen der App in Azure

1. In Visual Studio mit der Maustaste des Projekts im **Projektmappen-Explorer** , und wählen Sie **veröffentlichen** aus dem Kontextmenü.

2. Auf der **Veröffentlichungsziel** Seite **App Service** und dann **vorhandene auswählen**, und wählen Sie dann **veröffentlichen**.

    ![Wählen Sie eine Seite "Ziel veröffentlichen"](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/publish-select-existing-azure-app-service.png)

3. Falls Sie zuvor in Visual Studio Ihre Azure-Abonnement hinzugefügt haben, führen Sie die Schritte aus, auf dem Bildschirm. Die folgenden Schritte ermöglichen die Visual Studio für Azure-Abonnement, also, die mit die Liste der **Anwendungsdienste** Ihrer Website enthalten.

4. Auf der **App Service** Seite die **Abonnement** App Service hinzufügen. Klicken Sie unter **Ansicht**Option **Ressourcengruppe**. Erweitern Sie die Ressourcengruppe, die, der Sie App Service hinzugefügt, und wählen Sie dann die App Service. Wählen Sie **OK** zum Veröffentlichen der app.

5. Die **Ausgabe** Fenster zeigt, welche Bereitstellungsaktionen ausgeführt wurden, und meldet die erfolgreiche Durchführung der Bereitstellung.

6. Nach der erfolgreichen Bereitstellung wird der Standardbrowser automatisch an die URL der bereitgestellten Website geöffnet.

    ![Students_index_page_with_paging](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/cloud-app-browser.png)

    Ihre app wird jetzt in der Cloud ausgeführt werden.

An diesem Punkt die *SchoolContext* Datenbank wurde in der Azure SQL-Datenbank erstellt wurde, da Sie ausgewählt haben **Code First-Migrationen ausführen (wird beim Anwendungsstart ausgeführt)**. Die *"Web.config"* -Datei in die bereitgestellte Website geändert wurde, damit die [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) Initialisierer ausgeführt wird, zum ersten Mal Code liest oder schreibt Daten in der Datenbank (die aufgetreten sind Bei Auswahl der **Schüler/Studenten** Registerkarte "):

![Auszug aus der Konfigurationsdatei "Web.config"](https://asp.net/media/4367421/mig.png)

Der Bereitstellungsprozess erstellt auch eine neue Verbindungszeichenfolge *(SchoolContext\_DatabasePublish*) für Code First-Migrationen zum Aktualisieren des Datenbankschemas und das seeding der Datenbank.

![Verbindungszeichenfolge in der Datei "Web.config"](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)

Sie finden die bereitgestellte Version der Datei "Web.config" auf dem lokalen Computer in *ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*. Sie können die bereitgestellte zugreifen *"Web.config"* Datei selbst unter Verwendung von FTP. Anweisungen hierzu finden Sie unter [ASP.NET-webbereitstellung mithilfe von Visual Studio: Bereitstellen eines Codeupdates](xref:web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update). Befolgen Sie die Anweisungen, die mit beginnen "um ein FTP-Tool verwenden zu können, benötigen Sie drei Dinge: der FTP-URL, den Benutzernamen und das Kennwort."

> [!NOTE]
> Die Web-app implementieren nicht Sicherheit, damit jeder, der die URL findet die Daten ändern kann. Anweisungen dazu, wie Sie die Website zu schützen, finden Sie unter [bereitstellen eine sicheren ASP.NET MVC-app mit Mitgliedschaft, OAuth und SQL-Datenbank in Azure](/aspnet/core/security/authorization/secure-data). Sie können verhindern, dass andere Personen über die Website durch Beenden des Diensts über das Azure-Verwaltungsportal oder **Server-Explorer** in Visual Studio.

![Beenden Sie die app Service-Menüelement](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/server-explorer-stop-app-service.png)

## <a name="advanced-migrations-scenarios"></a>Erweiterte Migrationsszenarien

Wenn Sie eine Datenbank durch Ausführen von Migrationen automatisch durchführen, wie in diesem Tutorial gezeigt bereitstellen, und Sie bereitstellen auf einer Website, die auf mehreren Servern ausgeführt wird, konnte Sie mehrere Server, die beim Ausführen von Migrationen zur gleichen Zeit abgerufen werden. Migrationen sind atomisch –, also wenn zwei Server versuchen, die die gleiche Migration ausführen, eine erfolgreich ausgeführt, und die andere fehl schlägt, (vorausgesetzt, dass die Vorgänge können nicht zweimal ausgeführt werden). In diesem Szenario sollten Sie diese Probleme zu vermeiden können Sie Migrationen manuell aufrufen, und richten Ihren eigenen Code, sodass es nur einmal durchgeführt. Weitere Informationen finden Sie unter [ausgeführt wird und Scripting von Migrationen aus Code](http://romiller.com/2012/02/09/running-scripting-migrations-from-code/) Blog von Rowan Miller des und [Migrate.exe](/ef/ef6/modeling/code-first/migrations/migrate-exe) (für das Ausführen von Migrationen über die Befehlszeile).

Weitere Informationen zu anderen Migrationsszenarien finden Sie unter [Migrationen Screencast-Reihe](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx).

## <a name="code-first-initializers"></a>Code First-Initialisierer

In den Abschnitt zur Bereitstellung, Sie haben gesehen, die [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) Initialisierer, die verwendet wird. Code stellt zunächst auch weitere Initialisierungen, einschließlich ["createDatabaseIfNotExists"](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (Standardeinstellung), [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) (die Sie zuvor verwendet haben) und [ DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx). Die `DropCreateAlways` Initialisierer für das Einrichten von Bedingungen für die Komponententests nützlich sein kann. Sie können auch eigene Initialisierer schreiben, und Sie können keinen Initialisierer explizit aufrufen, wenn Sie nicht warten, bis die Anwendung liest bzw. in die Datenbank geschrieben werden sollen.

Weitere Informationen zu Initialisierern finden Sie unter [Grundlegendes zu Datenbank-Initialisierer in Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm) und Kapitel 6 des Buchs [Programming Entity Framework: Code First](http://shop.oreilly.com/product/0636920022220.do) von Julie Lerman und Rowan Miller.

## <a name="get-the-code"></a>Abrufen des Codes

[Herunterladen des abgeschlossenen Projekts](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie im [ASP.NET-Datenzugriff – empfohlene Ressourcen](xref:whitepapers/aspnet-data-access-content-map).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Aktivierte Code First-Migrationen
> * Bereitgestellt von der app in Azure (optional)

Wechseln Sie zum nächsten Artikel erfahren, wie Sie ein komplexeres Datenmodell für eine ASP.NET MVC-Anwendung zu erstellen.
> [!div class="nextstepaction"]
> [Erstellen eines komplexeren Datenmodells](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
