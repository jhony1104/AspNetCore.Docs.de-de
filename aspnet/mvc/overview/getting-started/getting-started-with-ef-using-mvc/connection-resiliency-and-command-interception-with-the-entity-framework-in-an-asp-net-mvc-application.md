---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Verwenden Sie die Verbindung resilienz und Befehl Abfangfunktion mit EF in einer ASP.NET MVC-app'
author: tdykstra
description: In diesem Tutorial erfahren Sie, wie Sie mit der Verbindung resilienz und Befehl abfangen. Sie sind zwei wichtige Funktionen von Entity Framework 6.
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: c89d809f-6c65-4425-a3fa-c9f6e8ac89f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 4ef13e676340f1bfac1a25219e6455c89b69df5e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837623"
---
# <a name="tutorial-use-connection-resiliency-and-command-interception-with-entity-framework-in-an-aspnet-mvc-app"></a>Tutorial: Verwenden Sie die Verbindung resilienz und Befehl Abfangfunktion mit Entity Framework in einer ASP.NET MVC-app

Bisher wurde die Anwendung lokal in IIS Express auf Ihrem Entwicklungscomputer ausgeführt wurde. Um eine reale Anwendung für andere Benutzer über das Internet verfügbar zu machen, müssen Sie sie auf einen Webhostinganbieter bereitstellen, und Sie zum Bereitstellen der Datenbank auf einen Datenbankserver verfügen.

In diesem Tutorial erfahren Sie, wie Sie mit der Verbindung resilienz und Befehl abfangen. Sie sind zwei wichtige Funktionen von Entity Framework 6, die besonders nützlich, wenn Sie in der Cloud-Umgebung bereitstellen: verbindungsresilienz (automatische Wiederholungsversuche nach vorübergehenden Fehlern) und Abfangen von Befehlen (Catch, die alle SQL-Abfragen an die Datenbank gesendet. um zu melden, oder ändern).

Dieses Tutorial Verbindung resilienz und Befehl Abfangfunktion ist optional. Wenn Sie dieses Tutorial überspringen, müssen einige kleinere Anpassungen in den nachfolgenden Tutorials vorgenommen werden.

In diesem Tutorial:

> [!div class="checklist"]
> * Aktivieren Sie die resilienz von Verbindungen
> * Abfangen von Befehlen zu aktivieren
> * Testen der neuen Konfiguration

## <a name="prerequisites"></a>Vorraussetzungen

* [Sortieren, Filtern und Auslagern](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-connection-resiliency"></a>Aktivieren Sie die resilienz von Verbindungen

Wenn Sie die Anwendung in Windows Azure bereitstellen, stellen Sie die Datenbank auf Windows Azure SQL-Datenbank einen Datenbank-Clouddienst bereit. Vorübergehenden Verbindungsfehlern sind in der Regel häufiger, wenn Sie eine Verbindung herstellen, um einen Clouddienst für die Datenbank oder Ihr Webserver und dem Datenbankserver direkt zusammen im selben Rechenzentrum angeschlossen sind. Auch wenn ein Cloud-Webserver und einen Clouddienst für die Datenbank in demselben Rechenzentrum gehostet werden, stehen weitere Netzwerkverbindungen zwischen ihnen, die Probleme, z. B. Load balancer haben kann.

Außerdem ist ein Cloud-Dienst in der Regel gemeinsam mit anderen Benutzern, was bedeutet, dass ihre Reaktionsfähigkeit, die von ihnen beeinflusst werden kann. Und Ihr Zugriff auf die Datenbank gelten möglicherweise die Drosselung. Einschränkung bedeutet, dass der Datenbank-Dienst löst Ausnahmen aus, wenn Sie versuchen, es häufig zugreifen, als in Ihrem Service Level Agreement (SLA) zulässig ist.

Viele oder die meisten Verbindungsprobleme, wenn Sie einen Cloud-Dienst zugreifen, sind vorübergehend, d. h. sie beheben sich selbst in kurzer Zeit. Also, wenn Sie versuchen Sie es einer Datenbank und einen Typ des Fehlers, der in der Regel vorübergehend ist, können Sie den Vorgang erneut versuchen, nach einer kurzen Wartezeit und der Vorgang erfolgreich sein kann. Sie können ein viel besseres Benutzererlebnis für Ihre Benutzer bereitstellen, wenn vorübergehende Fehler behandeln, indem versucht automatisch, die in diesem Fall die meisten von ihnen an den Kunden unsichtbar machen. Das verbindungsstabilitätsfeature in Entity Framework 6 automatisiert werden, dass der Vorgang einer wiederholten SQL-Abfragen ist fehlgeschlagen.

Das verbindungsstabilitätsfeature muss für eine bestimmte Datenbank-Dienst entsprechend konfiguriert werden:

- Er muss wissen, welche Ausnahmen wahrscheinlich vorübergehend sind. Fehler von einem vorübergehenden Dienstausfall verursacht werden, an der Netzwerkkonnektivität nicht die Ursache von Programmfehlern, z. B. Fehler wiederholt werden sollen.
- Er verfügt über eine entsprechende Zeitspanne zwischen zwei Wiederholungen einlegen, der einen fehlgeschlagenen Vorgang zu warten. Sie können mehr zwischen den Wiederholungsversuchen für einen Batchprozess warten, als Sie für eine Website können, in denen ein Benutzer auf eine Antwort wartet.
- Er verfügt über eine entsprechende Anzahl von Malen, bevor er wird abgebrochen wiederholt. Sie möchten möglicherweise weitere Male in einem Batchprozess zu wiederholen, die Sie in einer online-Anwendung verwenden würden.

Sie können konfigurieren, dass diese Einstellungen manuell für jede datenbankumgebung, die von Entity Framework-Anbieter unterstützt werden, aber die Standardwerte, die in der Regel gut für eine online-Anwendung arbeiten, die Windows Azure SQL-Datenbank verwendet wurden bereits für Sie konfiguriert und Dies sind die Einstellungen, die Sie für die Contoso University-Anwendung implementiert.

Alles, was Sie tun, um die resilienz von Verbindungen zu aktivieren ist eine Klasse erstellen, in der Assembly, die von abgeleitet der ["dbconfiguration"](https://msdn.microsoft.com/data/jj680699.aspx) Klasse, und legen Sie die SQL-Datenbank in dieser Klasse *Ausführungsstrategie*, was in EF eine andere Bezeichnung für *wiederholungsrichtlinie*.

1. Die DAL Ordner hinzufügen, eine Klassendatei namens *SchoolConfiguration.cs*.
2. Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

    Das Entity Framework führt den Code in eine abgeleitete Klasse gefundenen `DbConfiguration`. Können Sie die `DbConfiguration` Klasse, um Konfigurationsaufgaben im Code ausführen, die Sie andernfalls würde in die *"Web.config"* Datei. Weitere Informationen finden Sie unter [EntityFramework codebasierte Konfiguration](https://msdn.microsoft.com/data/jj680699).
3. In *StudentController.cs*, Hinzufügen einer `using` -Anweisung für `System.Data.Entity.Infrastructure`.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]
4. Ändern Sie alle von der `catch` blockiert, Catch `DataException` Ausnahmen, damit sie abfangen `RetryLimitExceededException` Ausnahmen stattdessen. Zum Beispiel:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=1)]

    Sie wurden mithilfe von `DataException` versuchen, Fehler zu identifizieren, die möglicherweise vorübergehende, um eine Meldung "versuchen Sie erneut" zu gewähren. Aber jetzt, dass Sie eine wiederholungsrichtlinie aktiviert haben, wird die eigentliche Ausnahme zurückgegebene umschlossen werden nur Fehler wahrscheinlich vorübergehend ist bereits ausprobiert und wurden Fehler mehrere Male und die `RetryLimitExceededException` Ausnahme.

Weitere Informationen finden Sie unter [Entity Framework-Verbindungsstabilität / Wiederholungslogik](https://msdn.microsoft.com/data/dn456835).

## <a name="enable-command-interception"></a>Abfangen von Befehlen zu aktivieren

Nun, dass Sie eine wiederholungsrichtlinie aktiviert haben, testen wie Sie, stellen Sie sicher, dass es wie erwartet funktioniert? Es ist nicht so leicht zu erzwingen, dass einen vorübergehenden Fehler erfolgen kann, insbesondere wenn Sie lokal ausführen, und es wäre besonders schwierig, tatsächliche vorübergehende Fehler in eine automatisierte Komponententests zu integrieren. Um das verbindungsstabilitätsfeature testen zu können, benötigen Sie eine Möglichkeit zum Abfangen von Abfragen, die Entity Framework in SQL Server sendet, und Ersetzen Sie die SQL Server-Antwort mit einem Ausnahmetyp, der in der Regel vorübergehend ist.

Sie können auch Abfangen der Abfrage verwenden, um eine bewährte Methode für Cloud-Anwendungen zu implementieren: [melden Sie sich die Latenz und Erfolg oder Fehler für alle Aufrufe von externen Diensten](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) wie z. B. Datenbankdienste. EF6 stellt einen [dedizierte protokollierungs-API](https://msdn.microsoft.com/data/dn469464) , die kann es einfacher, Protokollierung, aber in diesem Abschnitt des Tutorials erfahren Sie, wie Sie mit Entity Framework [Abfangfunktion](https://msdn.microsoft.com/data/dn469464) direkt, sowohl für Protokollierung und zum Simulieren von vorübergehender Fehlern.

### <a name="create-a-logging-interface-and-class"></a>Erstellen Sie eine protokollierungsschnittstelle und -Klasse

Ein [bewährte Methode für die Protokollierung](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) besteht darin, dies mithilfe einer Schnittstelle anstatt Aufrufe fest zu programmieren, System.Diagnostics.Trace-oder ein Logging-Klasse. Die erleichtert es Ihrer Protokollierungsmechanismus später zu ändern, wenn Sie dies tun müssen. In diesem Abschnitt Sie erstellen werden die protokollierungsschnittstelle und eine Klasse zum Implementieren von it./p >

1. Erstellen Sie einen Ordner im Projekt, und nennen Sie sie *Protokollierung*.
2. In der *Protokollierung* Ordner, erstellen Sie eine Klassendatei mit dem Namen *ILogger.cs*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    Die Schnittstelle enthält drei Ablaufverfolgungsstufen an, dass die relative Wichtigkeit der Protokolle und entwickelt, um die latenzzeitinformationen für externe Dienst-Aufrufe wie z. B. Datenbankabfragen bieten ein. Die Protokollierungsmethoden haben Überladungen, mit denen Sie die Ausnahme übergeben. Dies ist so, dass Ausnahmeinformationen einschließlich Stack Trace und der inneren Ausnahmen zuverlässig von der Klasse protokolliert wird, die implementiert die Schnittstelle, statt die standardremotedatenbank, die in jedem Methodenaufruf der Protokollierung in der gesamten Anwendung ausgeführt wird.

    Die TraceApi-Methoden können Sie die Latenz von jedem Aufruf an einen externen Dienst z. B. SQL-Datenbank nachverfolgen.
3. In der *Protokollierung* Ordner, erstellen Sie eine Klassendatei mit dem Namen *Logger.cs*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Die Implementierung verwendet System.Diagnostics für die Ablaufverfolgung. Dies ist ein integriertes Feature von .NET und das Generieren und nutzen Sie diese Daten erleichtert. Es gibt viele "Listener" Sie mit der System.Diagnostics-Ablaufverfolgung, Protokolle, Dateien, z. B. schreiben oder diese in Azure Blob Storage Schreiben verwenden können. Sehen Sie einige der Optionen und Links zu anderen Ressourcen für Weitere Informationen im [Problembehandlung für Azure-Websites in Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio). In diesem Tutorial, Sie nur Sie sich sehen werden, in der Visual Studio-Protokollen **Ausgabe** Fenster.

    In einer produktionsanwendung empfiehlt Ablaufverfolgung von Paketen als System.Diagnostics berücksichtigt, und der "ILogger"-Oberfläche ist es relativ einfach, einen anderen Nachverfolgungsmechanismus wechseln, wenn Sie dies tun möchten.

### <a name="create-interceptor-classes"></a>Erstellen Sie Interceptor-Klassen

Als Nächstes erstellen Sie die Klassen, denen das Entity Framework aufrufen wird jedes Mal, wenn darauf eine Abfrage an die Datenbank, eine vorübergehende Fehler zu simulieren und zu Protokollierung durchführen gesendet. Diese Interceptor-Klassen werden müssen, aus der `DbCommandInterceptor` Klasse. In diesen schreiben Sie die methodenüberschreibungen, die automatisch aufgerufen werden, wenn die Abfrage ausgeführt wird. In diesen Methoden können Sie untersuchen oder melden Sie sich die Abfrage, die an die Datenbank gesendet werden, und Sie können die Abfrage ändern, bevor sie auf die Datenbank gesendet wird oder etwas Zurückgeben von Entity Framework selbst ohne die Abfrage auch in der Datenbank zu übergeben.

1. Um den Interceptor-Klasse zu erstellen, die alle SQL-Abfrage protokolliert, die an die Datenbank gesendet wird, erstellen Sie eine Klassendatei namens *SchoolInterceptorLogging.cs* in die *DAL* Ordner, und Ersetzen Sie die Vorlage mit code Der folgende Code:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

    Für erfolgreiche Abfragen oder Befehle schreibt dieser Code ein Information-Protokoll mit Informationen zur Latenz. Für Ausnahmen wird ein Fehlerprotokoll erstellt.
2. Zum Erstellen der Interceptor-Klasse, die dummy vorübergehende Fehler generiert wird, bei der Eingabe "Throw" in der **Suche** erstellen eine Klassendatei namens *SchoolInterceptorTransientErrors.cs* in die *DAL* Ordner, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

    Dieser code nur überschreibt die `ReaderExecuting` -Methode, die für Abfragen aufgerufen wird, die mehrere Datenzeilen zurückgeben kann. Wenn Sie, überprüfen Sie die resilienz von Verbindungen für andere Typen von Abfragen möchten, können Sie auch überschreiben die `NonQueryExecuting` und `ScalarExecuting` Methoden, wie die Protokollierung-Interceptor ist.

    Wenn Sie führen Sie die Seite "Student" und "Throw", die als die zu suchende Zeichenfolge eingeben, erstellt der Code eine dummy-SQL-Datenbank-Ausnahme für Fehlernummer 20, eines Typs in der Regel vorübergehend bezeichnet. Andere Fehlernummern, die derzeit als vorübergehend erkannt werden 64, 233, 10053, 10054, 10060, 10928, 10929, 40197, 40501 und 40613, aber diese unterliegen in neuen Versionen von SQL-Datenbank.

    Der Code gibt die Ausnahme zurück, an Entity Framework anstelle der Ausführung der Abfrage, und übergeben die Abfrageergebnisse zurück. Die vorübergehende Ausnahme zurückgegeben viermal aus, und klicken Sie dann der Code wird auf das normale Verfahren von der Abfrage an die Datenbank übergeben.

    Da alles, was protokolliert wird, müssen Sie möglicherweise feststellen, dass Entity Framework versucht wird, um die Abfrage viermal auszuführen, bevor Sie erfolgreich, und des einzigen Unterschied in der Anwendung, dass es zum Rendern einer Seite mit Abfrageergebnissen länger dauert.

    Die Anzahl der Häufigkeit, mit die dem Entity Framework erneut auszuführen ist konfigurierbar. der Code gibt viermal aus, da dies der Standardwert für die Ausführungsrichtlinie für die SQL-Datenbank ist. Wenn Sie die Ausführungsrichtlinie ändern, mussten Sie auch ändern hier den Code ein, der angibt, wie oft ein vorübergehender Fehler generiert wird. Sie können auch den Code, um weitere Ausnahmen zu generieren, sodass Entity Framework eine Ausnahme auslöst, Ändern der `RetryLimitExceededException` Ausnahme.

    Der Wert in das Suchfeld eingegebenen werden in `command.Parameters[0]` und `command.Parameters[1]` (eine für den ersten Namen und eine für den Nachnamen verwendet wird). Wenn das Wert "% Throw %" gefunden wird, werden "Throw" in diese Parameter durch "an" ersetzt, damit einige Kursteilnehmer gefunden und zurückgegeben werden.

    Dies ist nur eine praktische Möglichkeit zum Testen von verbindungsresilienz basierend auf eine Eingabe zur Benutzeroberfläche der Anwendung ändern. Sie können auch Code, der vorübergehende Fehler für alle Abfragen oder Updates generiert schreiben, wie weiter unten in den Kommentaren zur Erläuterung der *DbInterception.Add* Methode.
3. In *"Global.asax"*, fügen Sie die folgenden `using` Anweisungen:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]
4. Fügen Sie die hervorgehobenen Zeilen, die `Application_Start` Methode:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=7-8)]

    Diese Codezeilen sind, was bewirkt, dass den Interceptor Code ausgeführt werden, wenn Entity Framework Abfragen an die Datenbank sendet. Beachten Sie, dass, da Sie separate Interceptor-Klassen für vorübergehende Fehler Simulation erstellt und Protokollierung können Sie unabhängig voneinander aktivieren und deaktivieren Sie diese.

    Können Sie mithilfe von Interceptors Hinzufügen der `DbInterception.Add` Methode eine beliebige Stelle im Code; es muss keine in der `Application_Start` Methode. Eine weitere Möglichkeit besteht, diesen Code in der DbConfiguration-Klasse, die Sie zuvor erstellt haben, um die Ausführungsrichtlinie zu konfigurieren.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=6-7)]

    Ganz egal, wo Sie diesen Code, einfügen, werden Sie darauf achten, nicht zum Ausführen `DbInterception.Add` für den gleichen Interceptor mehr als einmal, oder Sie zusätzliche interceptorinstanzen erhalten. Z. B. Wenn Sie den Interceptor Protokollierung zweimal hinzufügen, sehen Sie zwei Protokolle für alle SQL-Abfrage.

    Interceptors werden in der Reihenfolge Ihrer Registrierung ausgeführt (die Reihenfolge, in der `DbInterception.Add` Methode wird aufgerufen). Die Reihenfolge kann relevant sein, je nachdem, was Sie in der Interceptor tun. Beispielsweise kann ein Interceptor ändern den SQL-Befehl, die es, in abruft der `CommandText` Eigenschaft. Wenn sie den SQL-Befehl geändert wird, erhalten der nächsten Interceptor den geänderten SQL-Befehl, nicht den ursprünglichen SQL-Befehl.

    Sie haben die simulationscode vorübergehender Fehler in einer Weise geschrieben, die dazu führen, dass vorübergehende Fehler durch einen anderen Wert eingeben, in der Benutzeroberfläche ermöglicht. Als Alternative könnten Sie den Interceptor-Code, um die Reihenfolge der vorübergehende Ausnahmen immer zu generieren, ohne eine Überprüfung auf einen bestimmten Parameterwert schreiben. Anschließend können Sie den Interceptor hinzufügen, nur, wenn Sie vorübergehende Fehler generieren möchten. Wenn Sie dies tun, nicht jedoch Hinzufügen des Interceptors bis, nach Abschluss der Initialisierung der Datenbank. Führen Sie das heißt, mindestens eine Datenbank-Vorgang wie z. B. eine Abfrage auf einem von Ihrem Entitätenmengen auf, bevor Sie beginnen, vorübergehende Fehler generieren. Das Entity Framework führt mehrere Abfragen während der Initialisierung der Datenbank, und sie sind nicht in einer Transaktion ausgeführt, sodass Fehler während der Initialisierung den Kontext, der in einem inkonsistenten Zustand versetzen verursachen könnte.

## <a name="test-the-new-configuration"></a>Testen der neuen Konfiguration

1. Drücken Sie **F5** die Anwendung im Debugmodus ausführen, und klicken Sie auf die **Kursteilnehmer** Registerkarte.
2. Sehen Sie sich Visual Studio **Ausgabe** Fenster aus, um die Ausgabe der Ablaufverfolgung finden Sie unter. Möglicherweise müssen Scrollen Sie nach einigen JavaScript-Fehler, um die Protokolle geschrieben werden, indem Sie den Logger zu erhalten.

    Beachten Sie, dass Sie, die tatsächlichen SQL-Abfragen an die Datenbank gesendet sehen können. Sie sehen einige anfängliche Abfragen und Befehle, die Entity Framework ermöglicht damit beginnen, überprüfen die Version der Datenbank und migrationsverlaufstabelle (Migrationen im nächsten Tutorial erfahren Sie). Sie sehen, dass eine Abfrage für das Auslagern, um herauszufinden, wie viele Studenten vorhanden sind, und schließlich sehen Sie, die Abfrage, die für Schüler und Studentendaten abruft.

    ![Protokollierung für normale Abfrage](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)
3. In der **Schüler/Studenten** Seite als die zu suchende Zeichenfolge eingeben "Throw", und klicken Sie auf **Suche**.

    ![Lösen Sie die zu suchende Zeichenfolge](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

    Sie werden feststellen, dass der Browser ist nicht mehr reagiert für einige Sekunden, während der Entity Framework die Abfrage mehrere Male wiederholt. Der erste Wiederholungsversuch erfolgt sehr schnell, klicken Sie dann die Wartezeit vor dem erhöht sich, vor jedem Neuversuch zusätzliche. Dieser Prozess vor jedem erneuten Versuch aufgerufen wird, beim Warten auf mehr *exponentiell ansteigende Wartezeiten*.

    Wenn die Seite angezeigt wird, zeigt Schüler/Studenten mit "ein" in ihren Namen, sehen Sie sich das Fenster "Ausgabe", und sehen Sie, dass die gleiche Abfrage versucht wurde, fünfmal zuerst viermal zurückgebenden vorübergehende Ausnahmen. Für jede vorübergehender Fehler sehen Sie das Protokoll, den Sie, beim Generieren des vorübergehenden Fehlers in Schreiben der `SchoolInterceptorTransientErrors` -Klasse ("Returning vorübergehender Fehler für den Befehl"), und Sie sehen das Protokoll geschrieben werden, wenn `SchoolInterceptorLogging` Ruft die Ausnahme ab.

    ![Ausgabe der konsolenprotokollierung mit Wiederholungen](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

    Da Sie eine Suchzeichenfolge eingegeben haben, ist parametrisiert, die Abfrage, die für Schüler und Studentendaten zurückgibt:

    [!code-sql[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.sql)]

    Der Wert für die Parameter werden nicht protokolliert, jedoch kann. Wenn die Parameterwerte angezeigt werden sollen, können Sie mit der schreiben Protokollierungscode rufen Sie Parameterwerte aus der `Parameters` Eigenschaft der `DbCommand` -Objekt, das Sie in den interceptormethoden zu erhalten.

    Beachten Sie, dass Sie diesen Test nicht wiederholt werden können, es sei denn, Sie die Anwendung zu beenden und starten Sie ihn neu. Wenn Sie die resilienz von Verbindungen mehrere Male in einer einzelnen Ausführung der Anwendung testen möchten, könnten Sie Code zum Zurücksetzen des Zählers Fehler im Schreiben `SchoolInterceptorTransientErrors`.
4. Um den Unterschied die Ausführungsstrategie (wiederholungsrichtlinie) sendet, Kommentar der `SetExecutionStrategy` in Zeile *SchoolConfiguration.cs*studentenseite erneut im Debugmodus ausgeführt, und suchen Sie "Throw" erneut.

    Dieses Mal hält der Debugger zur ersten generierte Ausnahme, sofort verwendet werden, wenn er versucht, die Abfrage beim ersten ausführen.

    ![Dummy-Ausnahme](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)
5. Heben Sie die auskommentierung der *SetExecutionStrategy* in Zeile *SchoolConfiguration.cs*.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie im [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Verbindungsresilienz aktiviert
> * Abfangen von Befehlen mit aktiviert
> * Die neue Konfiguration getestet

Wechseln Sie zum nächsten Artikel erfahren Sie Code First-Migrationen und Azure-Bereitstellung.
> [!div class="nextstepaction"]
> [Code First-Migrationen und Azure-Bereitstellung](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)
