---
uid: signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
title: 'Tutorial: Serverübertragung mit ASP.NET SignalR 1.x | Microsoft-Dokumentation'
author: bradygaster
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet, um Server-broadcast-Funktionalität bereitzustellen. Serverübertragung bedeutet, dass das communic...
ms.author: bradyg
ms.date: 04/10/2013
ms.assetid: ab7b2554-956a-4f6d-b2a0-4ae0c62e8580
msc.legacyurl: /signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
msc.type: authoredcontent
ms.openlocfilehash: 258a55bf72c4b3425d001f478620fa9651952b3f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837467"
---
<a name="tutorial-server-broadcast-with-aspnet-signalr-1x"></a>Tutorial: Serverübertragung mit ASP.NET SignalR 1.x
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet, um Server-broadcast-Funktionalität bereitzustellen. Serverübertragung bedeutet, dass die Kommunikation, die an Clients gesendet, die vom Server initiiert werden. Dieses Szenario erfordert einen anderen Programmieransatz als Peer-zu-Peer-Szenarien, z. B. Chat-Anwendungen, die in denen Kommunikation, die an Clients gesendet, die von einer oder mehreren Clients initiiert werden.
> 
> Die Anwendung, die Sie in diesem Tutorial erstellen simuliert einen Börsenticker erstellen, ein typisches Szenario für die Funktion zum Server senden.
> 
> Kommentare, die auf dem Lernprogramm sind Willkommen. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Übersicht

Die [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet-Paket installiert eine simulierte Börsenticker-Anwendung in einem Visual Studio-Projekt. Im ersten Teil dieses Lernprogramms erstellen Sie eine vereinfachte Version der Anwendung von Grund auf neu. Im weiteren Verlauf des Lernprogramms Sie installieren das NuGet-Paket und überprüfen Sie die zusätzlichen Funktionen und den Code, der erstellt wird.

Der Börsenticker-Anwendung handelt es sich um einen Repräsentanten für eine Art von Echtzeit-Anwendung in der in regelmäßigen Abständen "Push" oder Broadcast, Benachrichtigungen vom Server an alle verbundenen Clients sollen.

Die Anwendung, die Sie im ersten Teil dieses Tutorials erstellen, zeigt ein Raster mit vordefinierten Daten.

![Erste StockTicker-version](tutorial-server-broadcast-with-aspnet-signalr/_static/image1.png)

Der Server wird in regelmäßigen Abständen nach dem Zufallsprinzip Aktienkurse aktualisiert, und der überträgt die Updates an alle verbundenen Clients. Im Browser die Zahlen und Symbole in der **ändern** und **%** Spalten dynamisch zu ändern, als Reaktion auf Benachrichtigungen vom Server. Wenn Sie weitere Browser auf die gleiche URL öffnen, werden sie alle die gleichen Daten und die gleichen Änderungen auf die Daten gleichzeitig.

In diesem Tutorial enthält die folgenden Abschnitte:

- [Erforderliche Komponenten](#prerequisites)
- [Erstellen des Projekts](#createproject)
- [Fügen Sie die SignalR-NuGet-Pakete hinzu.](#nugetpackages)
- [Richten Sie den Server-code](#server)
- [Richten Sie den Clientcode](#client)
- [Testen der Anwendung](#test)
- [Aktivieren der Protokollierung](#enablelogging)
- [Installieren Sie und überprüfen Sie das vollständige StockTicker-Beispiel](#fullsample)
- [Nächste Schritte](#nextsteps)

> [!NOTE]
> Wenn Sie nicht über die Schritte zum Erstellen der Anwendung arbeiten möchten, können Sie das Paket SignalR.Sample installieren, in einem neuen **leere ASP.NET-Webanwendung** Projekt, und Lesen Sie diese Schritte aus, um erläuterungen des Codes zu erleichtern. Der erste Teil des Lernprogramms umfasst eine Teilmenge des Codes SignalR.Sample aus, und der zweite Teil wird erläutert, die zusätzliche Funktionalität im Paket SignalR.Sample Schlüsselfunktionen.


<a id="prerequisites"></a>

## <a name="prerequisites"></a>Vorraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie Visual Studio 2012 oder 2010 SP1 auf Ihrem Computer installiert. Wenn Sie Visual Studio nicht haben, finden Sie unter [ASP.NET-Downloads](https://www.asp.net/downloads) , die kostenlose Visual Studio 2012 Express für Web zu erhalten.

Wenn Sie Visual Studio 2010 haben, stellen sicher, dass [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installiert ist.

<a id="createproject"></a>

## <a name="create-the-project"></a>Erstellen eines Projekts

1. Von der **Datei** klicken Sie im Menü **neues Projekt**.
2. In der **neues Projekt** Dialogfeld erweitern Sie **c#** unter **Vorlagen** , und wählen Sie **Web**.
3. Wählen Sie die **leere ASP.NET-Webanwendung** Vorlage, nennen Sie das Projekt *SignalR.StockTicker*, und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"](tutorial-server-broadcast-with-aspnet-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-nuget-packages"></a>Fügen Sie die SignalR-NuGet-Pakete

### <a name="add-the-signalr-and-jquery-nuget-packages"></a>Fügen Sie den SignalR und JQuery-NuGet-Pakete

Sie können die SignalR-Funktionen zu einem Projekt hinzufügen, durch die Installation von NuGet-Paket.

1. Klicken Sie auf **Extras | NuGet Paket-Manager | Paket-Manager Konsole**.
2. Geben Sie den folgenden Befehl aus, in der Paket-Manager.

    [!code-powershell[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample1.ps1)]

    Der SignalR-Paket installiert eine Reihe von anderen NuGet-Pakete als Abhängigkeiten. Wenn die Installation abgeschlossen ist, müssen Sie alle Server und Client-Komponenten erforderlich, um die SignalR-Komponente in einer ASP.NET-Anwendung einsetzen.

<a id="server"></a>

## <a name="set-up-the-server-code"></a>Richten Sie den Server-code

In diesem Abschnitt richten Sie den Code, der auf dem Server ausgeführt wird.

### <a name="create-the-stock-class"></a>Erstellen Sie die Stock-Klasse

Zunächst erstellen Sie die Stock-Model-Klasse, die Sie verwenden zum Speichern und Übertragen von Informationen zu einer Aktie.

1. Erstellen Sie eine neue Klassendatei im Projektordner, nennen Sie es *Stock.cs*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample2.cs)]

    Die beiden Eigenschaften, die Sie bei der Erstellung von Aktien festlegen werden das Symbol (z. B. "MSFT" bei Microsoft) und den Preis. Die anderen Eigenschaften hängen davon ab, wie und wann Sie Preis festlegen. Beim ersten, die Sie, Preis festlegen, ruft der Wert an DayOpen weitergegeben. Nachfolgende Aufrufe, wenn Sie festlegen, Preis, die Änderung und Prozent Eigenschaftswerte werden berechnet, basierend auf den Unterschied zwischen Preis- und DayOpen.

### <a name="create-the-stockticker-and-stocktickerhub-classes"></a>Erstellen der StockTicker und StockTickerHub-Klasse

Sie verwenden die SignalR-Hub-API, um die Server-zu-Client-Interaktion. Empfangen von Verbindungen und Methodenaufrufen von-Clients ist eine StockTickerHub-Klasse, die von SignalR-Hub-Klasse abgeleitet wird verarbeitet. Sie müssen auch vordefinierte Daten beizubehalten, und führen Sie ein Timer-Objekt, um in regelmäßigen Abständen preisaktualisierungen, unabhängig von Clientverbindungen auszulösen. Diese Funktionen können nicht in einer hubklasse, eingefügt werden, da der Hub-Instanzen vorübergehend sind. Eine Instanz des Hub-Klasse wird für jeden Vorgang auf den Hub, wie Verbindungen und Aufrufe vom Client an den Server erstellt. Daher ist der Mechanismus, der aktualisiert Preise, bleiben die vorhandene Daten und überträgt die preisaktualisierungen führen Sie in einer separaten Klasse, die Sie StockTicker nennen.

![Übertragungen von StockTicker](tutorial-server-broadcast-with-aspnet-signalr/_static/image4.png)

Sie möchten nur eine Instanz der StockTicker-Klasse, auf dem Server ausgeführt wird, daher Sie einen Verweis aus jeder StockTickerHub-Instanz in der StockTicker Singleton-Instanz eingerichtet müssen. Die StockTicker-Klasse hat in der Lage, die an Clients übertragen werden, da sie die vorhandenen Daten und löst Aktualisierungen, StockTicker ist jedoch keiner hubklasse. Aus diesem Grund muss die StockTicker-Klasse einen Verweis auf das Kontextobjekt für SignalR-Hub-Verbindung abzurufen. Sie können dann das Kontextobjekt für SignalR-Verbindung verwenden, an Clients senden.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **neues Element hinzufügen**.
2. Wenn Sie Visual Studio 2012 mit haben die [ASP.NET und Web Tools 2012.2 Update](https://go.microsoft.com/fwlink/?LinkId=279941), klicken Sie auf **Web** unter **Visual C#-** , und wählen Sie die **SignalR-Hubklasse** Elementvorlage. Wählen Sie andernfalls die **Klasse** Vorlage.
3. Nennen Sie die neue Klasse *StockTickerHub.cs*, und klicken Sie dann auf **hinzufügen**.

    ![Add StockTickerHub.cs](tutorial-server-broadcast-with-aspnet-signalr/_static/image5.png)
4. Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample3.cs)]

    Die [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) Klasse wird verwendet, um Methoden zu definieren, die Clients auf dem Server aufrufen können. Definieren Sie eine Methode: `GetAllStocks()`. Wenn ein Client zunächst mit dem Server verbunden ist, nenne sie diese Methode, um eine Liste aller die Aktien mit ihren aktuellen Preisen zu erhalten. Die Methode synchron ausgeführt werden und zurückgeben kann `IEnumerable<Stock>` , da sie Daten aus dem Arbeitsspeicher zurückgibt. Wenn die Methode die Daten abrufen, indem Sie die Aktionen ausgeführt werden, die dabei warten, z. B. einer Datenbanksuche oder einen Aufruf des Webdiensts, würden Sie angeben `Task<IEnumerable<Stock>>` als den Rückgabewert in die asynchrone Verarbeitung zu ermöglichen. Weitere Informationen finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server - beim asynchron ausführen](index.md).

    Das HubName-Attribut gibt an, wie der Hub in JavaScript-Code auf dem Client verwiesen wird. Der Standardnamen für den Client, wenn Sie dieses Attribut nicht verwenden, ist eine Version in Kamel-Schreibweise des Klassennamens, der in diesem Fall StockTickerHub würden.

    Wie Sie später sehen werden bei der Erstellung der StockTicker-Klasse, wird eine Singleton-Instanz dieser Klasse in der statischen Eigenschaft der Instanz erstellt. Im Arbeitsspeicher verbleibt, unabhängig davon, wie viele Clients eine Verbindung herstellen oder trennen, Singleton-Instanz besteht aus, und diese Instanz ist, was die GetAllStocks-Methode verwendet, um aktuelle Börseninformationen zu zurückzugeben.
5. Erstellen Sie eine neue Klassendatei im Projektordner, nennen Sie es *StockTicker.cs*, und Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample4.cs)]

    Da mehrere Threads dieselbe Instanz der StockTicker-Code ausgeführt werden, muss die Klasse besteht hinsichtlich sein.

    ### <a name="storing-the-singleton-instance-in-a-static-field"></a>Speichern die Singleton-Instanz in einem statischen Feld

    Der Code initialisiert die statische \_Instanzenfeld, das die Instance-Eigenschaft mit einer Instanz der Klasse, und dies wird ist die einzige Instanz der Klasse, die erstellt werden kann, da der Konstruktor als privat gekennzeichnet. [Verzögerte Initialisierung](https://msdn.microsoft.com/library/dd997286.aspx) wird verwendet, für die \_Instanzenfeld, nicht für andere Leistungsgründen jedoch sicherstellen, dass die instanzerstellung hinsichtlich ist.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample5.cs)]

    Jedes Mal, wenn einem Client und dem Server zwischen eine neue Instanz der Klasse StockTickerHub in einem separaten Thread ausgeführt StockTicker Ruft die Singletoninstanz ab aus der statischen StockTicker.Instance-Eigenschaft, wie Sie weiter oben in der Klasse StockTickerHub gesehen haben.

    ### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Das Speichern von vorhandenen Daten in einem ConcurrentDictionary

    Der Konstruktor initialisiert die \_Aktien-Auflistung mit einigen vordefinierten Beispieldaten und GetAllStocks zurückgibt, die Aktien. Wie Sie gesehen haben, wird diese Sammlung von Daten wiederum von StockTickerHub.GetAllStocks zurückgegeben, eine Servermethode, in der Hub-Klasse, die Clients aufrufen können.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample6.cs)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample7.cs)]

    Die Aktien-Sammlung ist definiert als eine [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) Typ für die Threadsicherheit. Als Alternative können Sie eine [Wörterbuch](https://msdn.microsoft.com/library/xfhwa508.aspx) -Objekt und das Wörterbuch explizit sperren, wenn Sie Änderungen vornehmen.

    Für diese beispielanwendung ist es gut zum Speichern von Anwendungsdaten im Arbeitsspeicher und Daten zu verlieren, wenn die StockTicker-Instanz freigegeben wird. In einer echten Anwendung würden Sie mit einem Back-End-Datenspeicher, z. B. einer Datenbank arbeiten.

    ### <a name="periodically-updating-stock-prices"></a>Aktienkurse wird in regelmäßigen Abständen aktualisiert.

    Der Konstruktor startet ein Timer-Objekt, das in regelmäßigen Abständen Methoden aufruft, die Aktienkurse nach dem Zufallsprinzip zu aktualisieren.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample8.cs)]

    UpdateStockPrices wird durch den Timer, aufgerufen, die NULL-Wert in den State-Parameter übergibt. Bevor Sie aktualisieren die Preise, ist eine Sperre auf die \_UpdateStockPricesLock-Objekt. Der Code überprüft, ob es sich bei einem anderen Thread ist bereits Preise aktualisieren und ruft dann TryUpdateStockPrice für jede Aktie in der Liste. Die Methode TryUpdateStockPrice entscheidet, ob den Aktienkurs zu ändern und wie viel, um ihn zu ändern. Wenn der Aktienkurs geändert wird, BroadcastStockPrice aufgerufen, um die Änderung der Aktienkurs an alle verbundenen Clients zu übertragen.

    Die \_UpdatingStockPrices Flag markiert, als [flüchtige](https://msdn.microsoft.com/library/x13ttww7.aspx) um sicherzustellen, dass der Zugriff auf diese hinsichtlich ist.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample9.cs)]

    In einer echten Anwendung würden TryUpdateStockPrice rufen die-Methode einen Webdienst, um den Preis zu suchen. In diesem Code verwendet er einen Zufallszahlen-Generator, um die Änderungen nach dem Zufallsprinzip vornehmen.

    ### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Abrufen von SignalR Kontext, damit die StockTicker-Klasse, die an Clients übertragen können

    Da die Preise variieren, hier in der StockTicker-Objekt stammen, ist dies das Objekt, das eine UpdateStockPrice-Methode auf alle verbundenen Clients aufgerufen werden muss. In einer hubklasse haben Sie eine API zum Aufrufen von Clientmethoden allerdings besteht nicht aus der Hub-Klasse abgeleitet und verfügt nicht über einen Verweis auf ein Hub-Objekt. Aus diesem Grund verfügt, um an verbundene Clients übertragen wird, die StockTicker-Klasse, die SignalR-Context-Instanz für die Klasse StockTickerHub abrufen und verwenden, die zum Aufrufen von Methoden auf Clients.

    Der Code Ruft einen Verweis auf den SignalR-Kontext ab, wenn es erstellt die Klasse Singletoninstanz übergibt, die auf verweisen an den Konstruktor, und der Konstruktor in die Clients-Eigenschaft eingefügt.

    Es gibt zwei Gründe, warum den Kontext nur einmal abgerufen werden soll: Abrufen des Kontexts ist ein aufwendiger Vorgang, und es einmal abrufen wird sichergestellt, dass die gewünschte Reihenfolge an Clients gesendeten Nachrichten beibehalten wird.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample10.cs)]

    Abrufen des Kontexts der Clients-Eigenschaft, und platzieren es in der StockTickerClient-Eigenschaft können Sie die Code zum Client Aufrufen von Methoden schreiben, der genauso aussieht wie in einer hubklasse. Um für alle Clients übertragen können Sie z. B. Clients.All.updateStockPrice(stock) schreiben.

    Die UpdateStockPrice-Methode, die Sie in BroadcastStockPrice aufrufen ist noch nicht vorhanden; Sie werden ihn später hinzufügen, wenn Sie Code schreiben, die auf dem Client ausgeführt wird. Sie können auf UpdateStockPrice hier verweisen, da Clients.All "Dynamic", ist dies bedeutet, dass der Ausdruck zur Laufzeit ausgewertet werden soll. Wenn dieser Methodenaufruf ausgeführt wird, SignalR wird der Name der Methode und der Wert des Parameters an den Client gesendet, und wenn der Client eine Methode namens UpdateStockPrice verfügt, diese Methode wird aufgerufen und der Wert des Parameters wird an ihn übergeben werden.

    Clients.All bedeutet, dass an alle Clients zu senden. SignalR bietet Ihnen weitere Optionen an die Clients oder Gruppen von Clients zu senden. Weitere Informationen finden Sie unter [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Registrieren Sie sich die SignalR-route

Der Server muss wissen, welche URL zum Abfangen und direkt an SignalR. Möchten Sie, dass Sie Code zum Hinzufügen der *"Global.asax"* Datei.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **neues Element hinzufügen**.
2. Wählen Sie die **Globale Anwendungsklasse** Elementvorlagen, und klicken Sie dann auf **hinzufügen**.

    ![Hinzufügen von "Global.asax"](tutorial-server-broadcast-with-aspnet-signalr/_static/image6.png)
3. Fügen Sie den SignalR-Route-Registrierungscode für die Anwendung\_Start-Methode:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample11.cs)]

    Standardmäßig ist die base-URL für den gesamten Datenverkehr von SignalR "/ Signalr", und "/ Signalr/Hubs" werden verwendet, um eine dynamisch generierte JavaScript-Datei abzurufen, die Proxys für alle Hubs definiert, Sie in Ihrer Anwendung haben. Die MapHubs-Methode enthält die Überladungen, mit denen Sie eine andere Basis-URL und bestimmte Optionen für SignalR in einer Instanz von angeben der [HubConfiguration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubconfiguration(v=vs.111).aspx) Klasse.
4. Hinzufügen einer using-Anweisung am Anfang der Datei:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample12.cs)]
5. Speichern und schließen Sie die *"Global.asax"* Datei, und erstellen Sie das Projekt.

Sie haben nun die Einrichtung der Servercode abgeschlossen. Im nächsten Abschnitt richten Sie den Client.

<a id="client"></a>

## <a name="set-up-the-client-code"></a>Richten Sie den Clientcode

1. Erstellen Sie eine neue HTML-Datei im Projektordner, und nennen Sie es *StockTicker.html*.
2. Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample13.html)]

    Der HTML-Code erstellt eine Tabelle mit 5 Spalten, eine Kopfzeile und eine Datenzeile mit einer einzigen Zelle, die alle 5 Spalten umfasst. Die Datenzeile zeigt "laden" und wird nur angezeigt werden, vorübergehend auf, wenn die Anwendung gestartet wird. JavaScript-Code wird diese Zeile zu entfernen und in seinen Zeilen direkt hinzugefügt werden, mit vordefinierten Daten, die vom Server abgerufen.

    Die Skripttags Geben Sie die jQuery-Skriptdatei, die SignalR Core-Skriptdatei, die Skriptdatei für SignalR-Proxys und eine StockTicker-Skriptdatei, die Sie später erstellen. Der SignalR-Proxys-Skriptdatei, die die URL "/ Signalr/Hubs" gibt an, wird dynamisch generiert und Proxymethoden für die Methoden für die hubklasse in diesem Fall für StockTickerHub.GetAllStocks definiert. Wenn Sie es vorziehen, können Sie diese JavaScript-Datei manuell generieren, mit [SignalR-Dienstprogramme](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) und deaktivieren Sie die dynamische Erstellung im Aufruf MapHubs-Methode.
3. > [!IMPORTANT]
   > Stellen Sie sicher, dass in die JavaScript-Datei verweist auf *StockTicker.html* richtig sind. Also stellen Sie sicher, dass die jQuery-Version in Ihr Skript-Tag (im Beispiel 1.8.2) identisch mit der jQuery-Version in Ihres Projekts ist *Skripts* Ordner, und stellen Sie sicher, dass die SignalR-Version in Ihr Skript-Tag SignalR identisch ist. Version Ihres Projekts *Skripts* Ordner. Ändern Sie die Dateinamen in den Skripttags, bei Bedarf.
4. In **Projektmappen-Explorer**, mit der rechten Maustaste *StockTicker.html*, und klicken Sie dann auf **als Startseite festlegen**.
5. Erstellen Sie eine neue JavaScript-Datei im Projektordner, und nennen Sie sie *StockTicker.js*...
6. Ersetzen Sie den Vorlagencode durch den folgenden Code:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample14.js)]

    $.connection bezieht sich auf die SignalR-Proxys. Der Code einen Verweis auf den Proxy für die StockTickerHub-Klasse abgerufen und in die Ticker-Variable eingefügt. Der Proxyname ist der Name, der durch das [HubName]-Attribut festgelegt wurde:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample15.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample16.cs)]

    Nachdem alle Variablen und Funktionen definiert wurden, initialisiert die letzte Zeile des Codes in der Datei die SignalR-Verbindung durch Aufrufen der SignalR-Startfunktion. Die Startfunktion wird asynchron ausgeführt und gibt eine [jQuery verzögerten Objekt](http://api.jquery.com/category/deferred-object/), das bedeutet, dass aufrufen kann, die done-Funktion zum Angeben der Funktion, die aufgerufen wird, wenn der asynchrone Vorgang abgeschlossen ist...

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample17.js)]

    Die "Init"-Funktion ruft die GetAllStocks-Funktion auf dem Server und verwendet die Informationen, die zum Aktualisieren der vorhandenen Tabelle gibt der Server zurück. Beachten Sie, dass in der Standardeinstellung verwenden Sie die Kamel-Schreibweise auf dem Client, obwohl der Methodenname Pascal-Schreibweise auf dem Server ist. Die Regel in Kamel-Schreibweise gilt nur für Methoden, Objekte nicht verwendet werden. Beispielsweise verweisen Sie in den Bestand. Symbol "und" Stock ". Preis, nicht stock.symbol oder stock.price.

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample18.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample19.cs)]

    Wenn Sie wollten Pascal-Schreibweise auf dem Client verwenden oder wenn Sie einen Namen für die ganz andere Methode verwenden möchten, Sie können ergänzen, um die Hub-Methode, mit dem Attribut HubMethodName die gleiche Weise versehen Sie die Hub-Klasse selbst, mit das HubName-Attribut.

    HTML-Code für eine Tabellenzeile wird erstellt in der Init-Methode für jedes Bestandsobjekt durch aufrufende FormatStock an Formateigenschaften des vorhandenen Objekts vom Server empfangen, und klicken Sie dann durch den Aufruf bereitgestellt (am oberen Rand definiert *StockTicker.js*) ersetzen Sie Platzhalter in der Variablen RowTemplate mit den Eigenschaftswerten Bestandsobjekt. Die resultierende HTML wird dann an die Lagerbestandstabelle angefügt.

    Sie rufen Init als eine Rückruffunktion, die ausgeführt wird, nach dem Abschluss der asynchronen Startfunktion übergibt. Wenn Sie als separate JavaScript-Anweisung nach dem Aufruf von Start Init aufgerufen wird, würde die Funktion fehl, da es sofort ausgeführt wird, ohne zu warten, für die Startfunktion, um den Vorgang abzuschließen, Herstellen der Verbindung. In diesem Fall würde die "Init"-Funktion versuchen, die GetAllStocks-Funktion aufrufen, bevor die Server-Verbindung hergestellt wird.

    Wenn der Server einen Kurs geändert wird, ruft es die UpdateStockPrice für verbundene Clients. Die Funktion wird der Client-Eigenschaft des Proxys StockTicker hinzugefügt, um sie vom Server an Aufrufe zur Verfügung stellen.

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample20.js)]

    Die Funktion UpdateStockPrice formatiert ein vordefiniertes Objekt, das vom Server empfangen in einer Zeile einer Tabelle die gleiche Weise wie in der Init-Funktion. Allerdings anstelle die Zeile an der Tabelle angefügt wird, sucht die Aktie der aktuellen Zeile in der Tabelle dabei diese Zeile durch die neue ersetzt.

<a id="test"></a>

## <a name="test-the-application"></a>Testen der Anwendung

1. Drücken Sie F5, um die Anwendung im Debugmodus auszuführen.

    Die Lagerbestandstabelle zunächst zeigt die "laden" die Zeile, klicken Sie dann nach einer kurzen Verzögerung, die die vordefinierten Anfangsdaten angezeigt werden, und starten Sie die Aktienkurse ändern.

    ![Laden](tutorial-server-broadcast-with-aspnet-signalr/_static/image7.png)

    ![Anfängliche Lagerbestandstabelle](tutorial-server-broadcast-with-aspnet-signalr/_static/image8.png)

    ![Lagerbestandstabelle Änderungen vom Server empfangen.](tutorial-server-broadcast-with-aspnet-signalr/_static/image9.png)
2. Kopieren Sie die URL aus der Adressleiste des Browsers, und fügen Sie ihn in eine oder mehrere neue Browser-Fenster.

    Die ursprüngliche Anzeige des vordefinierte entspricht dem ersten Browser, und Änderungen gleichzeitig vorgenommen werden.
3. Schließen Sie alle Browser, öffnen Sie einen neuen Browser, und wechseln Sie zu der gleichen URL.

    Der StockTicker-Singleton-Objekt weiterhin im-Server ausgeführt wird, damit das Lagerbestandstabelle zeigt, dass die Aktien weiterhin geändert haben. (Die ersten Tabelle mit 0 (null), die Zahlen ändern nicht angezeigt werden.)
4. Schließen Sie den Browser.

<a id="enablelogging"></a>

## <a name="enable-logging"></a>Protokollierung aktivieren

SignalR ist eine integrierte Protokollierung-Funktion, die Sie auf dem Client aktivieren können, um die Problembehandlung zu unterstützen. In diesem Abschnitt aktivieren Sie die Protokollierung und finden Sie Beispiele, die zeigen, wie Protokolle Sie feststellen, welche der folgenden Transportmethoden SignalR verwendet wird:

- [WebSockets](http://en.wikipedia.org/wiki/WebSocket), von IIS 8- und derzeit gebräuchlichen Browsern unterstützt.
- [Vom Server gesendeten Ereignisse](http://en.wikipedia.org/wiki/Server-sent_events), von Browsern als Internet Explorer unterstützt.
- [Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), wird von Internet Explorer unterstützt.
- [AJAX langer Abruf](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), wird von allen Browsern unterstützt.

Für eine bestimmte Verbindung wählt SignalR die beste Transportmethode, die dem Server und dem Client zu unterstützen.

1. Open *StockTicker.js* und fügen Sie eine Codezeile zum Aktivieren der Protokollierung unmittelbar vor dem Code, der initialisiert die Verbindung am Ende der Datei hinzu:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample21.js)]
2. Drücken Sie F5, um das Projekt auszuführen.
3. Öffnen Sie Ihren Browser die Developer-Tools-Fenster, und wählen Sie die Konsole, um die Protokolle anzuzeigen. Möglicherweise müssen die Seite zum Anzeigen der Protokolle von Signalr, die die Transportmethode für eine neue Verbindung aushandeln aktualisieren zu können.

    Wenn Sie Internet Explorer 10 unter Windows 8 (IIS 8) ausgeführt werden, ist die Transportmethode die WebSockets.

    ![IE 10 IIS 8-Konsole](tutorial-server-broadcast-with-aspnet-signalr/_static/image10.png)

    Wenn Sie Internet Explorer 10 unter Windows 7 (IIS 7.5) ausgeführt werden, ist die Transportmethode Iframe an.

    ![IE 10 Console, IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image11.png)

    Installieren Sie die Firebug-add-Ins rufen Sie ein Konsolenfenster, in Firefox. Wenn Sie Firefox 19 unter Windows 8 (IIS 8) ausgeführt werden, ist die Transportmethode die WebSockets.

    ![Firefox 19 IIS 8 Websockets](tutorial-server-broadcast-with-aspnet-signalr/_static/image12.png)

    Wenn Sie Firefox 19 unter Windows 7 (IIS 7.5) ausgeführt werden, ist die Transportmethode die vom Server gesendeten Ereignisse.

    ![Firefox 19-Konsole IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image13.png)

<a id="fullsample"></a>

## <a name="install-and-review-the-full-stockticker-sample"></a>Installieren Sie und überprüfen Sie das vollständige StockTicker-Beispiel

Der StockTicker-Anwendung, die installiert ist, indem die [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet-Paket umfasst mehr Funktionen als die vereinfachte Version an, die Sie gerade erstellt, von Grund auf neu haben. In diesem Abschnitt des Tutorials müssen Sie das NuGet-Paket zu installieren und überprüfen Sie die neuen Features und den Code, der sie implementiert.

### <a name="install-the-signalrsample-nuget-package"></a>Installieren Sie das SignalR.Sample NuGet-Paket

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **NuGet-Pakete verwalten**.
2. In der **NuGet-Pakete verwalten** Dialogfeld klicken Sie auf **Online**, geben Sie *SignalR.Sample* in die **Onlinesuche** ein, und klicken Sie dann auf  **Installieren Sie** in die **SignalR.Sample** Paket.

    ![Installieren Sie SignalR.Sample-Paket](tutorial-server-broadcast-with-aspnet-signalr/_static/image14.png)
3. In der *"Global.asax"* , kommentieren Sie die RouteTable.Routes.MapHubs() Ausgabekonfigurationsdatei Zeile, die Sie zuvor in der Anwendung hinzugefügt\_Start-Methode.

    Der Code in *"Global.asax"* ist nicht mehr benötigt werden, da das Paket SignalR.Sample die SignalR-Route im registriert die *App\_Start/RegisterHubs.cs* Datei:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample22.cs)]

    Im WebActivatorEx NuGet-Paket, das als eine Abhängigkeit von der SignalR.Sample-Paket installiert wird, ist die WebActivator-Klasse, die von der Assembly-Attribut verwiesen wird enthalten.
4. In **Projektmappen-Explorer**, erweitern Sie die *SignalR.Sample* Ordner, der durch die Installation des Pakets SignalR.Sample erstellt wurde.
5. In der *SignalR.Sample* Ordner mit der rechten Maustaste *StockTicker.html*, und klicken Sie dann auf **als Startseite festlegen**.

    > [!NOTE]
    > Installieren das SignalR.Sample NuGet Paket möglicherweise ändern, die Version von jQuery, die Sie in Ihrem *Skripts* Ordner. Die neue *StockTicker.html* -Datei, die in das Paket installiert die *SignalR.Sample* Ordner werden synchron mit der jQuery-Version, die das Paket installiert, aber wenn Sie, führen Sie den ursprünglichen möchten,*StockTicker.html* -Datei erneut, müssen Sie möglicherweise zuerst die jQuery-Verweises im Skripttag zu aktualisieren.

### <a name="run-the-application"></a>Ausführen der Anwendung

1. Drücken Sie F5, um die Anwendung auszuführen.

    Zusätzlich zum Raster, das Sie zuvor gesehen haben, zeigt die vollständige Börsenticker-Anwendung ein horizontalem Bildlauf Fenster, das dem vordefinierten dieselben Daten angezeigt. Wenn Sie die Anwendung zum ersten Mal ausführen, der "Markt" ist "geschlossen", und ein statischer Raster und ein Tickerfenster, das Durchführen eines Bildlaufs wird nicht angezeigt.

    ![Starten der StockTicker-Bildschirm](tutorial-server-broadcast-with-aspnet-signalr/_static/image15.png)

    Beim Klicken auf **Open Market**, **Börsenticker Live** Kästchen startet einen horizontalen Bildlauf durchführen, und der Server startet Aktienkurs Änderungen nach dem Zufallsprinzip in regelmäßigen Abständen zu senden. Jedes Mal, wenn einen Aktienkurs geändert wird, sowohl die **Live Stock Tabelle** Raster und die **Börsenticker Live** Feld aktualisiert werden. Wenn einer Aktie Preisänderung positiv ist, wird die Aktie mit einem grünen Hintergrund angezeigt, und wenn die Änderung negativ ist, wird die Aktie mit einem roten Hintergrund angezeigt.

    ![StockTicker-app Markt öffnen.](tutorial-server-broadcast-with-aspnet-signalr/_static/image16.png)

    Die **schließen Markt** beendet Sie die Änderungen und beendet den Ticker Bildlauf, und die **zurücksetzen** Schaltfläche Alle vordefinierte Daten auf den ursprünglichen Zustand zurückgesetzt, bevor preisänderungen gestartet. Wenn Sie weitere Browserfenster zu öffnen, und öffnen Sie die gleiche URL, sehen Sie die gleichen Daten, die zur gleichen Zeit in jedem Browser dynamisch aktualisiert. Wenn Sie eine der Schaltflächen klicken, Antworten von allen Browsern die gleiche Weise zur gleichen Zeit.

### <a name="live-stock-ticker-display"></a>Live Börsenticker-Anzeige

Die **Börsenticker Live** Anzeige ist eine ungeordnete Liste in ein Div-Element, das von CSS-Stilen in einer einzelnen Zeile formatiert ist. Der Ticker initialisiert und die gleiche Weise wie in der Tabelle aktualisiert: durch ersetzen die Platzhalter in einer &lt;li&gt; Vorlagenzeichenfolge und Dynamisches Hinzufügen von der &lt;li&gt; Elementen, die die &lt;Ul&gt; Element. Mithilfe der jQuery-animieren-Funktion verwenden, um die Margin-Left, von der unsortierten Liste in den Stilabschnitt variieren erfolgt der Bildlauf

Die Börsenticker HTML:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample23.html)]

Die Börsenticker CSS:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample24.html)]

Scrollen Sie die jQuery-Code, der es macht:

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample25.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Zusätzliche Methoden auf dem Server, den der Client aufrufen kann

Die Klasse StockTickerHub definiert vier zusätzliche Methoden, die der Client aufrufen kann:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample26.cs)]

OpenMarket, CloseMarket und Zurücksetzen der werden in Reaktion auf die Schaltflächen am oberen Rand der Seite aufgerufen. Sie veranschaulichen das Muster der ein Client auslösen geändert wird, die sofort an alle Clients weitergegeben wird. Jede dieser Methoden Ruft eine Methode in der StockTicker-Klasse, Auswirkungen der Markt Status ändern und überträgt dann den neuen Zustand.

In der StockTicker-Klasse wird der Zustand des Markts nach einer Eigenschaft MarketState verwaltet, die einen MarketState Enum-Wert zurückgibt:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample27.cs)]

Jede der Methoden, die Status der Markt ändern tun innerhalb eines Blocks Sperre, da die StockTicker-Klasse muss hinsichtlich sein:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample28.cs)]

Um sicherzustellen, dass dieser Code hinsichtlich, ist die \_MarketState-Feld, das hinter der Eigenschaft MarketState steht als flüchtig, markiert ist

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample29.cs)]

Die BroadcastMarketStateChange und BroadcastMarketReset Methoden ähneln die BroadcastStockPrice-Methode, die Sie bereits gesehen haben, außer sie unterschiedliche auf dem Client definierte Methoden aufrufen:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample30.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Zusätzliche Funktionen auf dem Client, den der Server aufrufen kann

Die UpdateStockPrice-Funktion verarbeitet jetzt sowohl das Raster und die Anzeige Ticker und jQuery.Color rote und grüne Farben Flash verwendet.

Neue Funktionen in *SignalR.StockTicker.js* aktivieren und deaktivieren Sie die Schaltflächen auf der Grundlage vermarkten Sie Status, und sie beenden oder starten, der horizontalen Bildlauf für Ticker Fenster. Da ticker.client, mehrere Funktionen hinzugefügt werden die [jQuery erweitern Funktion](http://api.jquery.com/jQuery.extend/) wird verwendet, um sie hinzuzufügen.

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample31.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Zusätzliche Client-Setup nach dem Herstellen der Verbindung

Nachdem der Client die Verbindung herstellt, es befindet sich einige zusätzliche: ermitteln, ob der Markt ist offen oder geschlossen ist, um die entsprechenden MarketOpened oder MarketClosed eine Funktion aufrufen, und fügen die Server-Methodenaufrufe auf Schaltflächen.

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample32.js)]

Die Servermethoden sind nicht dann mit den Schaltflächen bis verknüpft, nachdem die Verbindung hergestellt wurde, damit der Code testen kann nicht auf die Servermethoden aufrufen, bevor sie verfügbar sind.

<a id="nextsteps"></a>

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine SignalR-Anwendung programmieren, die Nachrichten vom Server an alle verbundenen Clients, die sowohl in regelmäßigen Abständen auch als Reaktion auf Benachrichtigungen von beliebigen Clients überträgt. Das Muster mit einer Multithread-Singleton-Instanz zur Beibehaltung des Zustands der Server kann außerdem auch in Multiplayer online game-Szenarien verwendet werden. Ein Beispiel finden Sie unter [das ShootR-Spiel, das basierend auf SignalR](https://github.com/NTaylorMullen/ShootR).

Tutorials, die Peer-zu-Peer-Kommunikationsszenarien veranschaulichen, finden Sie unter [erste Schritte mit SignalR](index.md) und [in Echtzeit mit SignalR aktualisieren](index.md).

Erweiterte Konzepte der SignalR-Entwicklung finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:

- [ASP.NET SignalR](https://asp.net/signalr/)
- [SignalR-Projekt](http://signalr.net/)
- [SignalR Github und Beispiele](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
