---
uid: signalr/overview/older-versions/dependency-injection
title: Abhängigkeitsinjektion in SignalR 1.x | Microsoft-Dokumentation
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/15/2013
ms.assetid: eaa206c4-edb3-487e-8fcb-54a3261fed36
msc.legacyurl: /signalr/overview/older-versions/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: c8aac09c3d3e06529f7834eb3f60dca2f3073922
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836292"
---
<a name="dependency-injection-in-signalr-1x"></a>Abhängigkeitsinjektion in SignalR 1.x
====================
durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Abhängigkeitsinjektion ist eine Möglichkeit zum Entfernen von hartcodierten Abhängigkeiten zwischen Objekten, erleichtert Ihnen die Abhängigkeiten eines Objekts entweder zu Testzwecken (mithilfe von Mockobjekten) zu ersetzen oder Laufzeitverhalten zu ändern. In diesem Tutorial wird gezeigt, wie Abhängigkeitsinjektion in SignalR-Hubs. Es wird gezeigt, wie IoC-Container mit SignalR verwenden. Ein IoC-Container ist ein allgemeines Rahmenwerk für Dependency Injection.

## <a name="what-is-dependency-injection"></a>Was ist Dependency Injection?

Überspringen Sie diesen Abschnitt, wenn Sie bereits über Dependency Injection vertraut sind.

*Abhängigkeitsinjektion* (DI) ist ein Muster, in dem sind Objekte nicht verantwortlich für das Erstellen ihrer eigenen Abhängigkeiten. Hier ist ein einfaches Beispiel DI motivieren. Nehmen wir an, dass Sie ein Objekt haben, die Meldungen protokolliert werden soll. Sie definieren eine protokollierungsschnittstelle:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

In das Objekt, Sie erstellen eine `ILogger` Protokollieren von Meldungen:

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Dies funktioniert, aber es ist dabei nicht um den optimalen Entwurf. Wenn Sie ersetzen möchten `FileLogger` mit einem anderen `ILogger` Implementierung müssen so ändern Sie `SomeComponent`. Angenommen, dass viele andere verwendet werden `FileLogger`, Sie müssen alle zu ändern. Oder wenn Sie sich entscheiden, stellen `FileLogger` ein Singleton, Sie müssen auch in der gesamten Anwendung ändern.

Ein besserer Ansatz ist auf "Einfügen" ein `ILogger` in das Objekt, z. B. durch einen Konstruktor mit dem Argument:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Nachdem das Objekt nicht verantwortlich für die Auswahl der ist `ILogger` verwenden. Sie können Swich `ILogger` Implementierungen, ohne die Objekte, die davon abhängen.

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Dieses Muster wird aufgerufen, [Konstruktorinjektion](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Ein weiteres Muster ist die Setter-Injektion, in dem Sie die Abhängigkeit über einen Setter-Methode oder Eigenschaft festgelegt.

## <a name="simple-dependency-injection-in-signalr"></a>Einfache Abhängigkeitsinjektion in SignalR

Betrachten Sie die Chat-Anwendung aus dem Lernprogramm [erste Schritte mit SignalR](../getting-started/tutorial-getting-started-with-signalr.md). So sieht die hubklasse über diese Anwendung aus:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Nehmen wir an, dass Chat-Nachrichten auf dem Server zu speichern, bevor diese gesendet werden soll. Sie definieren eine Schnittstelle, die diese Funktionalität abstrahiert und Dependency Injection zum Einfügen von der Schnittstelle verwenden möglicherweise die `ChatHub` Klasse.

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

Das einzige Problem ist, dass Hubs mit eine SignalR-Anwendung nicht direkt erstellen; SignalR, die sie für Sie erstellt haben. Standardmäßig erwartet SignalR eine Hub-Klasse über einen parameterlosen Konstruktor verfügt. Allerdings können Sie ganz einfach eine Funktion zum Erstellen von hubinstanzen registrieren und verwenden Sie diese Funktion zum Ausführen von DI. Registrieren Sie die Funktion durch den Aufruf **GlobalHost.DependencyResolver.Register**.

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

Jetzt SignalR dieser anonymen Funktion aufgerufen wird, sobald es zum Erstellen muss einer `ChatHub` Instanz.

## <a name="ioc-containers"></a>IoC-Container

Der vorherige Code ist in einfachen Fällen in Ordnung. Aber immer noch Folgendes schreiben musste:

[!code-css[Main](dependency-injection/samples/sample8.css)]

In einer komplexen Anwendung mit vielen Abhängigkeiten müssen Sie einen Großteil dieser "verknüpfen" Code schreiben zu können. Dieser Code kann schwierig zu verwalten sein, insbesondere dann, wenn Abhängigkeiten geschachtelt sind. Es ist auch schwer, Komponententests.

Eine Lösung ist einen IoC-Container verwenden. Ein IoC-Container ist eine Softwarekomponente, die zum Verwalten von Abhängigkeiten zuständig ist. Typen mit dem Container registrieren, und klicken Sie dann den Container verwenden, um Objekte zu erstellen. Der Container ermittelt automatisch die Abhängigkeit Beziehungen. Viele IoC-Container können auch steuern, z. B. Objektlebensdauer und Bereich.

> [!NOTE]
> "IoC" steht für "Inversion of Control", dies ist ein allgemeines Muster, in denen ein Framework in Anwendungscode aufruft. Ein IoC-Container erstellt die Objekte, die "die übliche ablaufsteuerung kehrt".


## <a name="using-ioc-containers-in-signalr"></a>Verwenden von IoC-Container in SignalR

Der Chat-Anwendung ist möglicherweise zu einfach, um von einem IoC-Container zu profitieren. Stattdessen sehen wir uns die [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) Beispiel.

Der StockTicker-Beispiel definiert zwei wichtigste Klassen:

- `StockTickerHub`: Die hubklasse, die Clientverbindungen verwaltet.
- `StockTicker`: Ein Singleton, der Aktienkurse enthält und in regelmäßigen Abständen aktualisiert werden.

`StockTickerHub` enthält einen Verweis auf die `StockTicker` Singleton, während `StockTicker` enthält einen Verweis auf die **IHubConnectionContext** für die `StockTickerHub`. Er verwendet diese Schnittstelle für die Kommunikation mit `StockTickerHub` Instanzen. (Weitere Informationen finden Sie unter [Serverübertragung mit ASP.NET SignalR](index.md).)

Wir können einen IoC-Container verwenden, um diese Abhängigkeiten ein wenig zu entwirren. Zunächst sehen wir vereinfachen die `StockTickerHub` und `StockTicker` Klassen. In den folgenden Code habe ich die Teile auskommentiert, dass wir nicht brauchen.

Entfernen Sie den parameterlosen Konstruktor aus `StockTicker`. Wir werden stattdessen immer DI verwenden, um den Hub zu erstellen.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

Entfernen Sie für StockTicker befindet die Singleton-Instanz. Später verwenden wir den IoC-Container, zur Steuerung der Lebensdauer der StockTicker befindet. Stellen Sie den Konstruktor außerdem öffentlich.

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

Als Nächstes können wir Umgestalten des Codes durch Erstellen einer Schnittstelle für `StockTicker`. Wir verwenden diese Schnittstelle zum Entkoppeln der `StockTickerHub` aus der `StockTicker` Klasse.

Visual Studio legt diese Art von Umgestaltung einfach. Öffnen Sie die Datei StockTicker.cs der rechten Maustaste auf die `StockTicker` Klassendeklaration, und wählen Sie **Umgestalten** ... **Schnittstelle extrahieren**.

![](dependency-injection/_static/image1.png)

In der **Schnittstelle extrahieren** Dialogfeld klicken Sie auf **Alles markieren**. Lassen Sie die anderen Standardwerte. Klicken Sie auf **OK**.

![](dependency-injection/_static/image2.png)

Visual Studio erstellt eine neue Schnittstelle namens `IStockTicker`, und ändert sich auch `StockTicker` für die Ableitung `IStockTicker`.

Öffnen Sie die Datei IStockTicker.cs und ändern Sie die Schnittstelle für **öffentliche**.

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

In der `StockTickerHub` Klasse, ändern Sie die beiden Instanzen von `StockTicker` zu `IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

Erstellen einer `IStockTicker` Schnittstelle ist nicht zwingend erforderlich, aber ich wollte, um anzuzeigen, wie Dependency Injection unterstützen kann, die Kopplung zwischen Komponenten in Ihrer Anwendung reduzieren.

## <a name="add-the-ninject-library"></a>Fügen Sie die Ninject-Bibliothek

Es gibt viele Open-Source-IoC-Container für .NET. In diesem Tutorial verwende ich [Ninject](http://www.ninject.org/). (Andere beliebten Bibliotheken enthalten [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), und [StructureMap ](http://docs.structuremap.net).)

Verwenden Sie NuGet Package Manager zum Installieren der [Ninject Bibliothek](https://nuget.org/packages/Ninject/3.0.1.10). In Visual Studio aus der **Tools** Menü die Option **NuGet Package Manager** > **-Paket-Manager-Konsole**. Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>Ersetzen Sie den Abhängigkeitskonfliktlöser für SignalR

Um Ninject in SignalR verwenden zu können, erstellen Sie eine abgeleitete Klasse **DefaultDependencyResolver**.

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

Diese Klasse überschreibt die **"GetService"** und **GetServices** Methoden **DefaultDependencyResolver**. SignalR ruft diese Methoden zum Erstellen von verschiedenen Objekten zur Laufzeit, einschließlich der Hub-Instanzen als auch verschiedene Dienste, die intern von SignalR verwendet.

- Die **"GetService"** Methode erstellt eine einzelne Instanz eines Typs. Überschreiben Sie diese Methode zum Aufrufen des Kernels Ninject **TryGet** Methode. Wenn diese Methode null zurückgibt, ein Fallback auf den Standardkonfliktlöser.
- Die **GetServices** Methode erstellt eine Auflistung von Objekten eines angegebenen Typs. Überschreiben Sie diese Methode, um die Ergebnisse mit den Ergebnissen der Standardkonfliktlöser Ninject verketten.

## <a name="configure-ninject-bindings"></a>Ninject Bindungen konfigurieren

Jetzt verwenden wir Ninject, um die Bindungen des Typs deklarieren.

Öffnen Sie die Datei RegisterHubs.cs. In der `RegisterHubs.Start` -Methode, Ninject-Container, der Ninject aufruft, erstellt die *Kernel*.

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

Erstellen Sie eine Instanz von unseren benutzerdefinierten Abhängigkeitskonfliktlöser:

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

Erstellen Sie eine Bindung für `IStockTicker` wie folgt:

[!code-html[Main](dependency-injection/samples/sample17.html)]

Dieser Code ist zwei Dinge sagen. Erste, wenn die Anwendung muss eine `IStockTicker`, der Kernel sollten erstellen Sie eine Instanz des `StockTicker`. Zweitens wird die `StockTicker` Klasse sollte als ein Singleton-Objekt erstellt werden. Ninject erstellt eine Instanz des Objekts, und die gleiche Instanz für jede Anforderung zurück.

Erstellen Sie eine Bindung für **IHubConnectionContext** wie folgt:

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

Dieser Code Creatres eine anonyme Funktion, die zurückgibt ein **IHubConnection**. Die **WhenInjectedInto** Methode weist Ninject, um diese Funktion zu verwenden, nur bei der Erstellung `IStockTicker` Instanzen. Der Grund ist, dass SignalR erstellt **IHubConnectionContext** Instanzen intern, und wir wollen nicht außer Kraft setzen, wie SignalR werden erstellt. Diese Funktion gilt nur für unsere `StockTicker` Klasse.

Übergeben Sie den Abhängigkeitskonfliktlöser in die **MapHubs** Methode:

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

Jetzt SignalR, verwenden Sie die im angegebenen Konfliktlösers wird **MapHubs**, anstatt des Standardkonfliktlösers.

Hier ist die vollständige codeauflistung für `RegisterHubs.Start`.

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

Um die Anwendung besteht in Visual Studio auszuführen, drücken Sie F5 aus. Navigieren Sie im Browserfenster zu `http://localhost:*port*/SignalR.Sample/StockTicker.html`.

![](dependency-injection/_static/image3.png)

Die Anwendung verfügt über genau die gleiche Funktionalität wie vor. (Eine Beschreibung finden Sie unter [Serverübertragung mit ASP.NET SignalR](index.md).) Wir noch nicht das Verhalten geändert werden. soeben erstellt den Code einfacher zu testen, verwalten und entwickeln.
