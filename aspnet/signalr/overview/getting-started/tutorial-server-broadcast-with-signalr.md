---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Tutorial: Serverübertragung mit SignalR 2 | Microsoft-Dokumentation'
author: tdykstra
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen.
ms.author: riande
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a6014e604613492db91b2dc6f846c3c73d938d99
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099298"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a>Tutorial: Server senden, die mit SignalR 2

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen. Serverübertragung bedeutet, dass der Server die Kommunikation an Clients gesendeten gestartet wird.

Die Anwendung, die Sie in diesem Tutorial erstellen simuliert einen Börsenticker erstellen, ein typisches Szenario für die Funktion zum Server senden. Der Server wird in regelmäßigen Abständen nach dem Zufallsprinzip Aktienkurse aktualisiert, und die Updates an alle verbundenen Clients übertragen. In den Browser, der Zahlen und Symbole in der **ändern** und **%** Spalten dynamisch zu ändern, als Reaktion auf Benachrichtigungen vom Server. Wenn Sie weitere Browser auf die gleiche URL öffnen, werden sie alle die gleichen Daten und die gleichen Änderungen auf die Daten gleichzeitig.

![Erstellen Sie web](tutorial-server-broadcast-with-signalr/_static/image1.png)

In diesem Tutorial haben Sie:

> [!div class="checklist"]
> * Erstellen eines Projekts
> * Richten Sie den Server-code
> * Überprüfen Sie den Servercode
> * Richten Sie den Clientcode
> * Überprüfen Sie den Clientcode
> * Testen der Anwendung
> * Protokollierung aktivieren

> [!IMPORTANT]
> Wenn Sie nicht über die Schritte zum Erstellen der Anwendung arbeiten möchten, können Sie das Paket SignalR.Sample in einem neuen leeren ASP.NET Web Application-Projekt installieren. Wenn Sie das NuGet-Paket installieren, ohne die Schritte in diesem Tutorial ausführen, müssen Sie die Anweisungen unter Befolgen der *"Readme.txt"* Datei. Zum Ausführen des Pakets müssen Sie eine OWIN-Startup hinzufügen wird Klasse die `ConfigureSignalR` -Methode in der das installierte Paket. Sie erhalten einen Fehler, wenn Sie nicht die OWIN-Startklasse hinzufügen. Finden Sie unter den [Installieren des Beispiels StockTicker](#install-the-stockticker-sample) Abschnitt dieses Artikels.


## <a name="prerequisites"></a>Vorraussetzungen

 * [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.

## <a name="create-the-project"></a>Erstellen eines Projekts

In diesem Abschnitt veranschaulicht, wie Visual Studio 2017 verwenden, um eine leere ASP.NET-Webanwendung zu erstellen.

1. Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.

    ![Erstellen Sie web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. In der **neue ASP.NET-Webanwendung – SignalR.StockTicker** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.

## <a name="set-up-the-server-code"></a>Richten Sie den Server-code

In diesem Abschnitt richten Sie den Code, der auf dem Server ausgeführt wird.

### <a name="create-the-stock-class"></a>Erstellen Sie die Stock-Klasse

Zunächst erstellen Sie die *Stock* Modellklasse, die Sie verwenden zum Speichern und Übertragen von Informationen zu einer Aktie.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.

1. Nennen Sie die Klasse *Stock* und fügen sie dem Projekt hinzu.

1. Ersetzen Sie den Code in die *Stock.cs* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    Die beiden Eigenschaften, die Sie bei der Erstellung von Aktien festlegen `Symbol` (z. B. "MSFT" bei Microsoft) und `Price`. Hängen Sie, dass die anderen Eigenschaften wie und wann Sie festlegen, `Price`. Beim ersten festlegen `Price`, ruft der Wert an weitergegeben `DayOpen`. Danach festlegen `Price`, berechnet die app die `Change` und `PercentChange` Eigenschaftswerte auf Grundlage der Unterschied zwischen `Price` und `DayOpen`.

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a>Erstellen Sie die StockTickerHub und StockTicker-Klasse

Sie verwenden die SignalR-Hub-API, um die Server-zu-Client-Interaktion. Ein `StockTickerHub` abgeleitete Klasse die `SignalRHub` Klasse übernimmt die Verbindungen und Methodenaufrufen von Clients empfangen. Sie müssen auch vordefinierte Daten beizubehalten, und führen Sie eine `Timer` Objekt. Die `Timer` Objekt löst in regelmäßigen Abständen preisaktualisierungen unabhängig von Clientverbindungen. Diese Funktionen können nicht eingefügt werden, einem `Hub` Klasse, da Hubs vorübergehend sind. Die app erstellt ein `Hub` Klasseninstanz für jeden Vorgang im Hub wie Verbindungen und Aufrufe vom Client an den Server. Daher ist der Mechanismus, der aktualisiert Preise, bleiben die vorhandene Daten und überträgt die preisaktualisierungen in einer separaten Klasse ausführen. Sie werden benennen Sie die Klasse `StockTicker`.

![Übertragungen von StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

Sollen nur eine Instanz der `StockTicker` Klasse, die auf dem Server ausgeführt wird, daher Sie einen Verweis aus jedem einrichten müssen `StockTickerHub` Instanz auf das Singleton `StockTicker` Instanz. Die `StockTicker` -Klasse verfügt über an Clients übertragen werden, da es die vorhandenen Daten und Updates, löst aber `StockTicker` wird keine `Hub` Klasse. Die `StockTicker` -Klasse verfügt über einen Verweis auf das Kontextobjekt für SignalR-Hub-Verbindung abrufen. Sie können dann das Kontextobjekt für SignalR-Verbindung verwenden, an Clients senden.

#### <a name="create-stocktickerhubcs"></a>Erstellen von StockTickerHub.cs

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - SignalR.StockTicker**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.

1. Nennen Sie die Klasse *StockTickerHub* und fügen sie dem Projekt hinzu.

    Dieser Schritt erstellt der *StockTickerHub.cs* Klassendatei. Gleichzeitig wird eine Reihe von Skriptdateien und Assemblyverweise, die von SignalR zum Projekt unterstützt.

1. Ersetzen Sie den Code in die *StockTickerHub.cs* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. Speichern Sie die Datei.

Die app verwendet die [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) Klasse, um Methoden zu definieren, die Clients auf dem Server aufrufen können. Definieren Sie eine Methode: `GetAllStocks()`. Wenn ein Client zunächst mit dem Server verbunden ist, nenne sie diese Methode, um eine Liste aller die Aktien mit ihren aktuellen Preisen zu erhalten. Die Methode synchron ausgeführt und zurückgeben kann `IEnumerable<Stock>` , da sie Daten aus dem Arbeitsspeicher zurückgibt.

Wenn die Methode die Daten abrufen, indem Sie die Aktionen ausgeführt werden, die dabei warten, z. B. einer Datenbanksuche oder einen Aufruf des Webdiensts, würden Sie angeben `Task<IEnumerable<Stock>>` als den Rückgabewert in die asynchrone Verarbeitung zu ermöglichen. Weitere Informationen finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server - beim asynchron ausführen](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).

Die `HubName` Attribut gibt an, wie die app Hub im JavaScript-Code auf dem Client verweist. Der Standardname auf dem Client, wenn Sie dieses Attribut nicht verwenden, ist Sie eine CamelCase-Version des Klassennamens, der in diesem Fall würden `stockTickerHub`.

Wie Sie später sehen werden bei der Erstellung der `StockTicker` -Klasse, die app erstellt eine Singleton-Instanz dieser Klasse in die statische `Instance` Eigenschaft. Diese Singletoninstanz des `StockTicker` befindet sich im Speicher, unabhängig davon, wie viele Clients eine Verbindung herstellen oder trennen. Diese Instanz ist, was die `GetAllStocks()` Methode verwendet, um die aktuellen vordefinierten Informationen zurückgegeben werden sollen.

#### <a name="create-stocktickercs"></a>Erstellen von StockTicker.cs

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.

1. Nennen Sie die Klasse *StockTicker* und fügen sie dem Projekt hinzu.

1. Ersetzen Sie den Code in die *StockTicker.cs* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

Da alle Threads dieselbe Instanz der StockTicker-Code ausgeführt werden, muss die StockTicker-Klasse threadsicher sein.

### <a name="examine-the-server-code"></a>Überprüfen Sie den Servercode

Wenn Sie den Server-Code untersuchen, wird sie Ihnen das Verständnis der Funktionsweise der app helfen.

#### <a name="storing-the-singleton-instance-in-a-static-field"></a>Speichern die Singleton-Instanz in einem statischen Feld

Der Code initialisiert die statische `_instance` Feld, das sichert die `Instance` Eigenschaft mit einer Instanz der Klasse. Da der Konstruktor privat ist, ist es die einzige Instanz der Klasse, die die app erstellen kann. Die app verwendet [verzögerte Initialisierung](/dotnet/framework/performance/lazy-initialization) für die `_instance` Feld. Es ist nicht zur Verbesserung der Leistung. Es ist, stellen Sie sicher, dass die Erstellung der Instanz threadsicher ist.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

Jedes Mal, die ein Client eine mit dem Server Verbindung eine neue Instanz der Ausführung in einem separaten Thread StockTickerHub-Klasse ruft die Singleton-Instanz besteht aus den `StockTicker.Instance` statische Eigenschaft ist, wie Sie gesehen haben werden weiter oben in der `StockTickerHub` Klasse.

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Das Speichern von vorhandenen Daten in einem ConcurrentDictionary

Der Konstruktor initialisiert die `_stocks` Auflistung mit einigen vordefinierten Beispieldaten und `GetAllStocks` gibt die Aktien. Wie Sie gesehen haben, wird diese Sammlung von Daten durch zurückgegeben `StockTickerHub.GetAllStocks`, dies ist eine Servermethode in der `Hub` -Klasse, die Clients aufrufen können.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

Die Aktien-Sammlung ist definiert als eine [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) Typ für die Threadsicherheit. Als Alternative können Sie eine [Wörterbuch](https://msdn.microsoft.com/library/xfhwa508.aspx) -Objekt und das Wörterbuch explizit sperren, wenn Sie Änderungen vornehmen.

Für diese beispielanwendung ist es gut zum Speichern von Anwendungsdaten im Arbeitsspeicher und Daten zu verlieren, wenn die app freigibt, die `StockTicker` Instanz. In einer echten Anwendung würden Sie mit einem Back-End-Datenspeicher, z. B. eine Datenbank arbeiten.

#### <a name="periodically-updating-stock-prices"></a>Aktienkurse wird in regelmäßigen Abständen aktualisiert.

Der Konstruktor gestartet wird, wird eine `Timer` -Objekt, das in regelmäßigen Abständen Methoden aufruft, die Aktienkurse nach dem Zufallsprinzip zu aktualisieren.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 `Timer` Aufrufe `UpdateStockPrices`, die NULL-Wert in den State-Parameter übergibt. Bevor Sie aktualisieren die Preise, akzeptiert die app eine Sperre für die `_updateStockPricesLock` Objekt. Der Code überprüft, ob ein anderer Thread wird bereits Preise aktualisiert, und klicken Sie dann ruft `TryUpdateStockPrice` für jede Aktie in der Liste. Die `TryUpdateStockPrice` Methode entschieden, ob den Aktienkurs zu ändern und wie viel, um ihn zu ändern. Wenn der Aktienkurs geändert wird, wird die app ruft `BroadcastStockPrice` senden, die die Änderung der Aktienkurs für alle verbundenen Clients.

Die `_updatingStockPrices` Flag festgelegt [flüchtige](https://msdn.microsoft.com/library/x13ttww7.aspx) um sicherzustellen, dass es die Thread-sicher ist.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

In einer realen Anwendung die `TryUpdateStockPrice` Methodenaufruf einen Webdienst, um den Preis zu suchen. In diesem Code verwendet die app eine Zufallszahlen-Generators nach dem Zufallsprinzip ändern.

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Abrufen von SignalR Kontext, damit die StockTicker-Klasse, die an Clients übertragen können

Da die Preise variieren hier aus kommen die `StockTicker` Objekt, es ist das Objekt, das aufgerufen werden muss ein `updateStockPrice` Methode für alle verbundenen Clients. In einer `Hub` -Klasse haben Sie eine API zum Aufrufen von Clientmethoden, aber `StockTicker` nicht abgeleitet der `Hub` Klasse und verfügt nicht über einen Verweis auf einen `Hub` Objekt. Übertragung für verbundene Clients, die `StockTicker` -Klasse, die zum Abrufen der Instanz des SignalR-Kontext für verfügt über die `StockTickerHub` Klasse, und verwenden, die zum Aufrufen von Methoden auf Clients.

Der Code Ruft einen Verweis auf den SignalR-Kontext ab, wenn es erstellt die Klasse Singletoninstanz übergibt, die auf verweisen an den Konstruktor und der Konstruktor fügt sie in der `Clients` Eigenschaft.

Es gibt zwei Gründe, warum den Kontext nur einmal abgerufen werden soll: Abrufen des Kontexts wird als teuer, und es abrufen, sobald sorgt dafür, dass die app die vorgesehene Reihenfolge der Nachrichten an die Clients beibehält.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

Abrufen der `Clients` Eigenschaft des Kontexts und der Implementierung der `StockTickerClient` Eigenschaft können Sie Code zum Client Aufrufen von Methoden schreiben, der genauso aussieht wie in einer `Hub` Klasse. Z. B. für alle Clients, die Sie senden können schreiben `Clients.All.updateStockPrice(stock)`.

Die `updateStockPrice` -Methode, die Sie in Aufrufen `BroadcastStockPrice` noch nicht vorhanden ist. Sie werden ihn später hinzufügen, wenn Sie Code schreiben, die auf dem Client ausgeführt wird. Sehen Sie sich `updateStockPrice` hier da `Clients.All` "Dynamic", d. h., die app wird den Ausdruck zur Laufzeit ausgewertet wird. Wenn dieser Methodenaufruf ausgeführt wird, SignalR wird der Name der Methode und der Wert des Parameters an den Client gesendet, und wenn der Client eine Methode namens hat `updateStockPrice`, die app diese Methode aufzurufen und der Wert des Parameters an es übergeben wird.

`Clients.All` bedeutet, dass für alle Clients senden. SignalR bietet Ihnen weitere Optionen an die Clients oder Gruppen von Clients zu senden. Weitere Informationen finden Sie unter [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Registrieren Sie sich die SignalR-route

Der Server muss wissen, welche URL zum Abfangen und direkt an SignalR. Zu diesem Zweck fügen Sie eine OWIN-Startup-Klasse hinzu:

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - SignalR.StockTicker** wählen **installiert** > **Visual C#**   >  **Web** und Wählen Sie dann **OWIN-Startklasse**.

1. Nennen Sie die Klasse *Start* , und wählen Sie **OK**.

1. Ersetzen Sie den Standardcode in der *"Startup.cs"* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

Sie haben nun das Einrichten der Servercode abgeschlossen. Im nächsten Abschnitt richten Sie den Client.

## <a name="set-up-the-client-code"></a>Richten Sie den Clientcode

In diesem Abschnitt richten Sie den Code, der auf dem Client ausgeführt wird.

### <a name="create-the-html-page-and-javascript-file"></a>Erstellen Sie die HTML-Seite und die JavaScript-Datei

Die HTML-Seite zeigt die Daten und die JavaScript-Datei werden die Daten zu organisieren.

#### <a name="create-stocktickerhtml"></a>Erstellen von StockTicker.html

Zunächst fügen Sie den HTML-Client.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.

1. Nennen Sie die Datei *StockTicker* , und wählen Sie **OK**.

1. Ersetzen Sie den Standardcode in der *StockTicker.html* Datei durch den folgenden Code:

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    Der HTML-Code erstellt eine Tabelle mit fünf Spalten, eine Kopfzeile und eine Datenzeile mit einer einzigen Zelle, die alle fünf Spalten umfasst. Die Datenzeile zeigt "laden" vorübergehend auf, wenn die app gestartet wird. JavaScript-Code wird diese Zeile zu entfernen und in seinen Zeilen direkt hinzugefügt werden, mit vordefinierten Daten, die vom Server abgerufen.

    Geben Sie die Skripttags:

    * Die jQuery-Skriptdatei.

    * Die SignalR Core-Skriptdatei.

    * Die Skriptdatei für den SignalR-Proxys.

    * Eine StockTicker-Skriptdatei, die Sie später erstellen.

    Die app generiert dynamisch die Skriptdatei für SignalR-Proxys. Es gibt die URL "/ Signalr/Hubs", und definiert Methoden für die Methoden für die Hub-Klasse, in diesem Fall Proxy für `StockTickerHub.GetAllStocks`. Wenn Sie es vorziehen, können Sie diese JavaScript-Datei manuell generieren, mit [SignalR-Dienstprogramme](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/). Vergessen Sie nicht, deaktivieren Sie die dynamische Erstellung in der `MapHubs` Methodenaufruf.

1. In **Projektmappen-Explorer**, erweitern Sie **Skripts**.

    Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.

    > [!IMPORTANT]
    > Der Paket-Manager wird eine höhere Version von SignalR-Skripts installieren.

1. Aktualisieren Sie die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *StockTicker.html*, und wählen Sie dann **als Startseite festlegen**.

#### <a name="create-stocktickerjs"></a>Erstellen von StockTicker.js

Erstellen Sie jetzt die JavaScript-Datei.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **JavaScript-Datei**.

1. Nennen Sie die Datei *StockTicker* , und wählen Sie **OK**.

1. Fügen Sie folgenden Code, der *StockTicker.js* Datei:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a>Überprüfen Sie den Clientcode

Wenn Sie die Client-Code untersuchen, können es Sie erfahren Sie, wie der Clientcode mit den Servercode, um die app funktioniert stellen interagiert.

#### <a name="starting-the-connection"></a>Die Verbindung starten

`$.connection` bezieht sich auf die SignalR-Proxys. Der Code Ruft einen Verweis auf den Proxy für die `StockTickerHub` -Klasse und fügt es in der `ticker` Variable. Die Proxyname ist der Name, die festgelegt wurde, indem die `HubName` Attribut:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

Nachdem Sie die Variablen und Funktionen definieren, initialisiert die letzte Zeile des Codes in der Datei die SignalR-Verbindung durch Aufrufen von SignalR `start` Funktion. Die `start` Funktion wird asynchron ausgeführt und gibt eine [jQuery verzögerten Objekt](http://api.jquery.com/category/deferred-object/). Sie können zum Angeben der Funktion aufrufen, wenn die Anwendung die asynchrone Aktion wurde die done-Funktion aufrufen.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a>Abrufen aller die Aktien

Die `init` Funktionsaufrufe der `getAllStocks` Funktionen auf dem Server und verwendet die Informationen, die zum Aktualisieren der vorhandenen Tabelle gibt der Server zurück. Beachten Sie, dass, in der Standardeinstellung CamelCasing auf dem Client verwendet, obwohl der Methodenname Pascal-Schreibweise auf dem Server ist. Die Regel CamelCasing gilt nur für Methoden, Objekte nicht verwendet werden. Beispielsweise finden Sie `stock.Symbol` und `stock.Price`, nicht `stock.symbol` oder `stock.price`.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

In der `init` -Methode, die app erstellt HTML für eine Tabellenzeile für jedes Bestandsobjekt durch den Aufruf vom Server empfangen `formatStock` Format Eigenschaften der `stock` Objekt aus, und klicken Sie dann durch den Aufruf `supplant` ersetzen Sie Platzhalter in der `rowTemplate` Variable mit dem die `stock` objekteigenschaftswerte. Die resultierende HTML wird dann an die Lagerbestandstabelle angefügt.

> [!NOTE]
> Rufen Sie `init` durch Übergabe in als eine `callback` -Funktion, die nach dem asynchronen führt `start` Funktion abgeschlossen ist. Wenn Sie aufgerufen `init` als separate JavaScript-Anweisung nach dem Aufruf `start`, die Funktion würde fehlschlagen, da sie sofort ausführen würde, ohne zu warten, für die Startfunktion, um den Vorgang abzuschließen, Herstellen der Verbindung. In diesem Fall die `init` Funktion aufrufen, versucht der `getAllStocks` funktionieren, bevor die app eine Verbindung hergestellt wird.

#### <a name="getting-updated-stock-prices"></a>Abrufen von aktualisierten Aktienkurse

Wenn der Server einen Kurs geändert wird, ruft er die `updateStockPrice` auf verbundenen Clients. Die app fügt die Funktion hinzu, um die Clienteigenschaft des der `stockTicker` Proxy für diese Aufrufe vom Server zur Verfügung.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

Die `updateStockPrice` -Funktion formatiert, die ein vordefiniertes Objekt, das in eine Tabelle vom Server empfangene Zeile die gleiche Weise wie in der `init` Funktion. Anstelle die Zeile an der Tabelle angefügt wird, sucht die Aktie der aktuellen Zeile in der Tabelle, und diese Zeile durch die neue ersetzt.

## <a name="test-the-application"></a>Testen der Anwendung

Sie können testen, die app, um sicherzustellen, dass es funktioniert. Sie sehen, dass alle Browserfenster die live Lagerbestandstabelle mit Aktienkurse schwankt angezeigt werden soll.

1. In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um die app im Debugmodus auszuführen.

    ![Screenshot der Benutzer das Aktivieren von debugging-Modus und Play auswählen.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    Ein Browserfenster wird geöffnet und zeigt die **Live Stock Tabelle**. Die vordefinierte Tabelle zeigt anfänglich der Zeile "laden", klicken Sie dann nach kurzer Zeit, die app zeigt die vordefinierten Anfangsdaten, und starten Sie die Aktienkurse ändern.

1. Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.

    Die ursprüngliche Anzeige des vordefinierte entspricht dem ersten Browser, und Änderungen gleichzeitig vorgenommen werden.

1. Schließen Sie alle Browser, öffnen Sie einen neuen Browser und navigieren Sie zu der gleichen URL.

    Der StockTicker-Singleton-Objekt weiterhin auf dem Server ausgeführt wurde. Die **Live Stock Tabelle** zeigt an, die die Aktien weiterhin geändert haben. Daraufhin nicht die ersten Tabelle mit 0 (null), die Zahlen ändern.

1. Schließen Sie den Browser.

## <a name="enable-logging"></a>Protokollierung aktivieren

SignalR ist eine integrierte Protokollierung-Funktion, die Sie auf dem Client aktivieren können, um die Problembehandlung zu unterstützen. In diesem Abschnitt aktivieren Sie die Protokollierung und finden Sie Beispiele, die zeigen, wie Protokolle Sie feststellen, welche der folgenden Transportmethoden SignalR verwendet wird:

* [WebSockets](http://en.wikipedia.org/wiki/WebSocket), von IIS 8- und derzeit gebräuchlichen Browsern unterstützt.

* [Vom Server gesendeten Ereignisse](http://en.wikipedia.org/wiki/Server-sent_events), von Browsern als Internet Explorer unterstützt.

* [Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), wird von Internet Explorer unterstützt.

* [AJAX langer Abruf](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), wird von allen Browsern unterstützt.

Für eine bestimmte Verbindung wählt SignalR die beste Transportmethode, die dem Server und dem Client zu unterstützen.

1. Open *StockTicker.js*.

1. Fügen Sie zum Aktivieren der Protokollierung unmittelbar vor dem Code, der die Verbindung am Ende der Datei initialisiert diese hervorgehobene Codezeile hinzu:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. Drücken Sie **F5** um das Projekt auszuführen.

1. Öffnen Sie Ihren Browser die Developer-Tools-Fenster, und wählen Sie die Konsole, um die Protokolle anzuzeigen. Möglicherweise müssen die Seite zum Anzeigen der Protokolle von SignalR, die die Transportmethode für eine neue Verbindung aushandeln aktualisieren zu können.

    * Wenn Sie Internet Explorer 10 unter Windows 8 (IIS 8) ausführen, wird die Transportmethode die **WebSockets**.

    * Wenn Sie Internet Explorer 10 unter Windows 7 (IIS 7.5) ausführen, wird die Transportmethode die **Iframe**.

    * Wenn Sie Firefox 19 unter Windows 8 (IIS 8) ausführen, wird die Transportmethode die **WebSockets**.

        > [!TIP]
        > Installieren Sie die Firebug-add-Ins rufen Sie ein Konsolenfenster, in Firefox.

    * Wenn Sie Firefox 19 unter Windows 7 (IIS 7.5) ausführen, wird die Transportmethode die **vom Server gesendeten** Ereignisse.

## <a name="install-the-stockticker-sample"></a>Installieren des Beispiels StockTicker

Die [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installiert die Anwendung besteht. Das NuGet-Paket umfasst mehr Funktionen als die vereinfachte Version an, der Sie von Grund auf neu erstellt. In diesem Abschnitt des Tutorials müssen Sie das NuGet-Paket zu installieren und überprüfen Sie die neuen Features und den Code, der sie implementiert.

> [!IMPORTANT]
> Wenn Sie das Paket installieren, ohne die vorherigen Schritten in diesem Tutorial auszuführen, müssen Sie eine OWIN-Startklasse zu Ihrem Projekt hinzufügen. Diese Datei "Readme.txt" des NuGet-Pakets wird dieser Schritt erläutert.

### <a name="install-the-signalrsample-nuget-package"></a>Installieren Sie das SignalR.Sample NuGet-Paket

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. In **NuGet-Paket-Manager: SignalR.StockTicker**Option **Durchsuchen**.

1. Von **Paketquelle**Option **nuget.org**.

1. Geben Sie *SignalR.Sample* in das Suchfeld ein, und wählen **Microsoft.AspNet.SignalR.Sample** > **installieren**.

1. In **Projektmappen-Explorer**, erweitern Sie die *SignalR.Sample* Ordner.

    Installieren des Pakets SignalR.Sample erstellt den Ordner und seinen Inhalt.

1. In der *SignalR.Sample* Ordner mit der rechten Maustaste *StockTicker.html*, und wählen Sie dann **als Startseite festlegen**.

    > [!NOTE]
    > Installieren das SignalR.Sample NuGet Paket möglicherweise ändern, die Version von jQuery, die Sie in Ihrem *Skripts* Ordner. Die neue *StockTicker.html* -Datei, die in das Paket installiert die *SignalR.Sample* Ordner werden synchron mit der jQuery-Version, die das Paket installiert, aber wenn Sie, führen Sie den ursprünglichen möchten,*StockTicker.html* -Datei erneut, müssen Sie möglicherweise zuerst die jQuery-Verweises im Skripttag zu aktualisieren.

### <a name="run-the-application"></a>Ausführen der Anwendung

 Die Tabelle, die Sie in der ersten app gesehen haben, mussten nützliche Features. Die vollständige Börsenticker-Anwendung zeigt die neuen Features: ein horizontalem Bildlauf Fenster, das zeigt die vordefinierten Daten und die Aktien, die Farbe zu ändern, da sie steigen und fallen.

1. Drücken Sie **F5**, um die App auszuführen.

     Wenn Sie die app zum ersten Mal ausführen, die "Markt" ist "geschlossen", und eine statische Tabelle und ein Tickerfenster, das Durchführen eines Bildlaufs wird nicht angezeigt.

1. Wählen Sie **Open Market**.

    ![Screenshot von der live-Ticker.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * Die **Börsenticker Live** Kästchen startet einen horizontalen Bildlauf durchführen, und der Server startet Aktienkurs Änderungen nach dem Zufallsprinzip in regelmäßigen Abständen zu senden.

    * Jedes Mal ein Aktienkurs geändert wird, die app aktualisiert werden sowohl die **Stock-Live-Tabelle** und **Börsenticker Live**.

    * Bei einer Aktie Preisänderung positiv ist, zeigt die app die Aktie mit einem grünen Hintergrund an.

    * Wenn die Änderung negativ ist, zeigt die app die Aktie mit einem roten Hintergrund.

1. Wählen Sie **schließen Markt**.

    * In der Tabelle aktualisiert beenden.

    * Der Ticker Bildlauf beendet wird.

1. Wählen Sie **zurücksetzen**.

    * Alle vorhandene Daten wird zurückgesetzt.

    * Die app wird den anfänglichen Zustand vor dem ersten preisänderungen wiederhergestellt.

1. Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.

1. Sie sehen die gleichen Daten, die zur gleichen Zeit in jedem Browser dynamisch aktualisiert.

1. Wenn Sie eines der Steuerelemente auswählen, Antworten von allen Browsern die gleiche Weise zur gleichen Zeit.

### <a name="live-stock-ticker-display"></a>Live Börsenticker-Anzeige

Die **Börsenticker Live** Anzeige ist eine ungeordnete Liste in eine `<div>` Element in einer einzelnen Zeile CSS-Formatvorlagen formatiert. Die app initialisiert und aktualisiert den Ticker die gleiche Weise wie in der Tabelle: durch ersetzen die Platzhalter in einer `<li>` Vorlagenzeichenfolge und Dynamisches Hinzufügen von der `<li>` Elementen, die die `<ul>` Element. Die app enthält, Durchführen eines Bildlaufs durch mithilfe der jQuery `animate` Funktion variieren die Margin-Left, von der unsortierten Liste innerhalb der `<div>`.

#### <a name="signalrsample-stocktickerhtml"></a>SignalR.Sample StockTicker.html

Die Börsenticker HTML-Code:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a>SignalR.Sample StockTicker.css

Die Börsenticker CSS-Code:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a>SignalR.Sample SignalR.StockTicker.js

Scrollen Sie die jQuery-Code, der es macht:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Zusätzliche Methoden auf dem Server, den der Client aufrufen kann

Es gibt weitere Methoden, die die app aufrufen kann, zur Steigerung der Flexibilität für die app.

#### <a name="signalrsample-stocktickerhubcs"></a>SignalR.Sample StockTickerHub.cs

Die `StockTickerHub` -Klasse definiert vier zusätzliche Methoden, die der Client aufrufen kann:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

Die app ruft `OpenMarket`, `CloseMarket`, und `Reset` als Reaktion auf die Schaltflächen am oberen Rand der Seite. Sie veranschaulichen das Muster der ein Client eine Änderung am Zustand sofort an alle Clients weitergegeben auslösen. Jede dieser Methoden Ruft eine Methode in der `StockTicker` Klasse, die bewirkt, dass die statusänderung Markt und überträgt dann den neuen Zustand.

#### <a name="signalrsample-stocktickercs"></a>SignalR.Sample StockTicker.cs

In der `StockTicker` -Klasse, die app verwaltet den Zustand des Markts mit einem `MarketState` Eigenschaft, die zurückgibt eine `MarketState` Enum-Wert:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

Jede der Methoden, die den Markt-Status zu ändern ist dies innerhalb eines Blocks für die Sperre der `StockTicker` Klasse threadsicher sein muss:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

Um sicherzustellen, dass dieser Code ist threadsicher, die `_marketState` Feld, das sichert die `MarketState` bezeichneten `volatile`:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

Die `BroadcastMarketStateChange` und `BroadcastMarketReset` Methoden ähneln die BroadcastStockPrice-Methode, die Sie bereits gesehen haben, außer sie unterschiedliche auf dem Client definierte Methoden aufrufen:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Zusätzliche Funktionen auf dem Client, den der Server aufrufen kann

Die `updateStockPrice` Funktion verarbeitet jetzt sowohl in der Tabelle und die Ticker-Anzeige, und es verwendet `jQuery.Color` rote und grüne Farben Flash.

Neue Funktionen in *SignalR.StockTicker.js* aktivieren und deaktivieren Sie die Schaltflächen, die basierend auf den Markt Zustand. Sie starten oder beenden Sie auch die **Börsenticker Live** horizontalen Bildlauf. Da viele Funktionen hinzugefügt werden `ticker.client`, die app verwendet die [jQuery erweitern Funktion](http://api.jquery.com/jQuery.extend/) hinzufügen.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Zusätzliche Client-Setup nach dem Herstellen der Verbindung

Nachdem der Client die Verbindung herstellt, hat es einige zusätzliche Schritte zu führen:

* Ermitteln, ob der Markt offen oder geschlossen ist, rufen Sie die entsprechende ist `marketOpened` oder `marketClosed` Funktion.

* Fügen Sie die Server-Methodenaufrufe auf Schaltflächen an.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

Die Servermethoden sind nicht mit den Schaltflächen bis verknüpft, nachdem die app die Verbindung hergestellt wird. Dies ist der Code die Servermethoden aufrufen kann, bevor sie verfügbar sind.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In diesem Tutorial haben Sie gelernt, wie Sie eine SignalR-Anwendung programmieren, die Nachrichten vom Server an alle verbundenen Clients zu übertragen. Jetzt können Sie Nachrichten in regelmäßigen Abständen und als Reaktion auf Benachrichtigungen von beliebigen Clients übertragen. Sie können das Konzept der Multithread-Singleton-Instanz verwenden, zur Beibehaltung des Zustands der Server in Multiplayer online game-Szenarien. Ein Beispiel finden Sie unter [ShootR Spiel auf der Grundlage von SignalR](https://github.com/NTaylorMullen/ShootR).

Tutorials, die Peer-zu-Peer-Kommunikationsszenarien veranschaulichen, finden Sie unter [erste Schritte mit SignalR](introduction-to-signalr.md) und [in Echtzeit mit SignalR aktualisieren](tutorial-high-frequency-realtime-with-signalr.md).

Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:

* [ASP.NET SignalR](../../index.md)
* [SignalR-Projekt](http://signalr.net/)
* [SignalR GitHub und Beispiele](https://github.com/SignalR/SignalR)
* [SignalR-Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie:

> [!div class="checklist"]
> * Erstellen des Projekts
> * Richten Sie den Server-code
> * Überprüft den Servercode
> * Richten Sie den Clientcode
> * Überprüft den Client-code
> * Testen der Anwendung
> * Aktiviert die Protokollierung

Wechseln Sie zum nächsten Artikel erfahren Sie, wie eine Echtzeit-Anwendung zu erstellen, die ASP.NET SignalR 2 verwendet.
> [!div class="nextstepaction"]
> [Erstellen von Echtzeit-Web-app mit SignalR](real-time-web-applications-with-signalr.md)