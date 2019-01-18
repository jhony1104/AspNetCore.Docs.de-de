---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-net-client
title: ASP.NET SignalR-für-API-Hubs - Client für .NET (c#) | Microsoft-Dokumentation
author: pfletcher
description: Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für den SignalR .NET-Client zu erhalten, wie z. B. Windows Store (WinRT), WPF, Silverlight und Nachteile Version 2...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: 6d02d9f7-94e5-4140-9f51-5a6040f274f6
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: 9981b8b91be3395b1a3aa7e0cabb1b7f455d47be
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396180"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-c"></a>ASP.NET SignalR-für-API-Hubs - Client für .NET (c#)
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für den SignalR .NET-Client zu erhalten, wie z. B. Windows Store (WinRT), WPF, Silverlight und konsolenanwendungen Version 2.
>
> Der SignalR-Hubs-API können Sie die Remoteprozeduraufrufe (RPCs) von einem Server verbundene Clients und von den Clients an den Server vornehmen. Im Server-Code Sie Methoden definieren, die von Clients aufgerufen werden können, und rufen Sie Methoden, die auf dem Client ausgeführt. Im Clientcode Sie Methoden definieren, die vom Server aufgerufen werden können, und rufen Sie Methoden, die auf dem Server ausgeführt. SignalR ist für alle Client-zu-Server sich für Sie übernimmt.
>
> SignalR bietet außerdem eine Low-Level-API wird aufgerufen, dauerhafte Verbindungen. Eine Einführung in die SignalR-Hubs und dauerhafte Verbindungen oder für ein Lernprogramm, das zeigt, wie Sie eine vollständige SignalR-Anwendung erstellen, finden Sie unter [SignalR - erste Schritte](../getting-started/index.md).
>
> ## <a name="software-versions-used-in-this-topic"></a>In diesem Thema verwendeten Softwareversionen
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> - .NET 4.5
> - SignalR-Version 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Vorherige Versionen dieses Themas
>
> Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).

## <a name="overview"></a>Übersicht

Dieses Dokument enthält folgende Abschnitte:

- [Client-Setup](#clientsetup)
- [Gewusst wie: Herstellen einer Verbindung](#establishconnection)

    - [Domänenübergreifende Verbindungen von Silverlight-clients](#slcrossdomain)
- [Gewusst wie: Konfigurieren der Verbindung](#configureconnection)

    - [Die maximale Anzahl gleichzeitiger Verbindungen in WPF-Clients festlegen.](#maxconnections)
    - [Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter](#querystring)
    - [Das Angeben der Transportmethode](#transport)
    - [Gewusst wie: Angeben von HTTP-Header](#httpheaders)
    - [Gewusst wie: Geben Sie Clientzertifikate](#clientcertificate)
- [Vorgehensweise: Erstellen Sie die Hubproxy-Klasse](#proxy)
- [Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können](#callclient)

    - [Methoden ohne Parameter](#clientmethodswithoutparms)
    - [Methoden mit Parametern, Parametertypen angeben](#clientmethodswithparmtypes)
    - [Methoden mit Parametern, dynamische Objekte für die Parameter angeben](#clientmethodswithdynamparms)
    - [Vorgehensweise beim Entfernen eines Handlers](#removehandler)
- [Gewusst wie: Servermethoden vom Client aufrufen.](#callserver)
- [Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung](#connectionlifetime)
- [Gewusst wie: Behandeln von Fehlern](#handleerrors)
- [Gewusst wie: Aktivieren der clientseitigen Protokollierung](#logging)
- [WPF, Silverlight und Konsolenanwendung Codebeispiele für Clientmethoden, die der Server aufgerufen werden können](#wpfsl)

Ein .NET Client Beispielprojekte finden Sie in den folgenden Ressourcen:

- [Gustavo-Armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) auf "github.com" (app-Beispiele in WinRT, Silverlight-Konsole).
- [DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) auf GitHub.com (WPF-Beispiel).
- [SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) auf "github.com" (Console app-Beispiel).

Dokumentation zum Programmieren des Servers oder Clients mit JavaScript finden Sie in den folgenden Ressourcen:

- [SignalR-Hubs-API-Guide - Server](hubs-api-guide-server.md)
- [SignalR-Hubs-API-Leitfaden – JavaScript-Client](hubs-api-guide-javascript-client.md)

Links zu Themen,-API-Referenz sind, .NET 4.5-Version der API. Wenn Sie .NET 4 verwenden, finden Sie unter [.NET 4-Version der API-Themen](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="clientsetup"></a>

## <a name="client-setup"></a>Client-setup

Installieren Sie die [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet-Paket (nicht die [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) Paket). Dieses Paket unterstützt WinRT, Silverlight, WPF, Konsolenanwendung und Windows Phone-Clients, die für .NET 4 und .NET 4.5.

Ist die Version von SignalR, die Sie auf dem Client haben sich von der Version, die Sie auf dem Server haben, ist SignalR häufig auf den Unterschied anpassen. Ein Server mit SignalR 2-Version unterstützt z. B. Clients, die 1.1.x installiert haben, sowie Clients, die Version 2 installiert haben. Wenn der Unterschied zwischen der Version auf dem Server und die Version auf dem Client zu groß ist oder SignalR löst aus, wenn der Client neuer als der Server ist, eine `InvalidOperationException` Ausnahme aus, wenn der Client versucht, eine Verbindung herzustellen. Die Fehlermeldung lautet "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Gewusst wie: Herstellen einer Verbindung

Bevor Sie eine Verbindung herstellen können, müssen Sie erstellen eine `HubConnection` Objekt, und erstellen Sie einen Proxy. Rufen Sie zum Herstellen einer Verbindung, die `Start` Methode für die `HubConnection` Objekt.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> JavaScript-Clients müssen Sie mindestens einen Ereignishandler vor dem Aufruf registriert die `Start` Methode zum Herstellen der Verbindung. Dies ist nicht für .NET-Clients erforderlich. Für JavaScript-Clients, erstellt des generierte Proxycodes-Proxys für alle Hubs, die vorhanden sind automatisch auf dem Server, und Registrieren eines Handlers ist, geben Sie die Hubs an der Client verwenden will. Aber für einen .NET Client Hub Proxys manuell erstellt, damit SignalR wird davon ausgegangen, dass Sie eine Hub-Instanz verwendet werden, die Sie über einen Proxy für erstellen.


Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service. WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).

Wenn die `Start` Methode asynchron ausgeführt wird. Um sicherzustellen, dass es sich bei nachfolgenden Codezeilen nicht erst ausführen, nachdem die Verbindung hergestellt ist, verwenden Sie `await` in einer asynchronen Methode von ASP.NET 4.5 oder `.Wait()` in eine synchrone Methode. Verwenden Sie keine `.Wait()` in einem WinRT-Client.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a>Domänenübergreifende Verbindungen von Silverlight-clients

Informationen dazu, wie Sie die domänenübergreifende Verbindungen von Silverlight-Clients zu aktivieren, finden Sie unter [vornehmen einer Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Gewusst wie: Konfigurieren der Verbindung

Bevor Sie eine Verbindung herstellen, können Sie eine der folgenden Optionen angeben:

- Gleichzeitige Verbindungen beschränken.
- Abfragezeichenfolgenparameter.
- Die Transportmethode.
- HTTP-Header.
- Clientzertifikate.

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a>Die maximale Anzahl gleichzeitiger Verbindungen in WPF-Clients festlegen.

In WPF-Clients müssen Sie möglicherweise die maximale Anzahl gleichzeitiger Verbindungen vom Standardwert 2 erhöhen. Der empfohlene Wert ist 10.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

Weitere Informationen finden Sie unter [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter

Wenn Sie möchten die Daten an den Server gesendet, wenn der Client eine Verbindung herstellt, können Sie Abfragezeichenfolgen-Parameter an das Verbindungsobjekt hinzufügen. Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter in Client-Code festlegen.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample5.cs)]

Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter im Servercode zu lesen.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Das Angeben der Transportmethode

Als Teil der Prozess der verbindungsherstellung verhandelt ein SignalR-Client normalerweise mit dem Server um den besten Transport zu bestimmen, der unterstützt wird, indem sowohl Server-als auch. Wenn Sie bereits wissen, dass Transport Sie verwenden möchten, können Sie dieser Aushandlungsprozess umgehen. Um die Transportmethode anzugeben, übergeben Sie einen Transport-Objekt an die Start-Methode. Das folgende Beispiel zeigt, wie die Transportmethode im Clientcode angegeben wird.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

Die [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) Namespace enthält die folgenden Klassen, die Sie verwenden können, an den Transport.

- [LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)
- [ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)
- [WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (nur verfügbar, wenn sowohl Server-als auch .NET 4.5 verwenden.)
- [AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (wählt automatisch den besten Transport, die von dem Client und dem Server unterstützt wird. Dies ist der Standardtransport. Übergeben dies in der `Start` Methode hat dieselbe Wirkung wie das alles nicht übergeben.)

Der Transport ForeverFrame ist nicht in dieser Liste enthalten, da er nur von Browsern verwendet wird.

Informationen zum Überprüfen der Transportmethode in Server-Code finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server – zum Abrufen von Informationen über den Client aus der Kontexteigenschaft](hubs-api-guide-server.md#contextproperty). Weitere Informationen zu Transporten und Fallbacks, finden Sie unter [Einführung zu SignalR - Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a>Gewusst wie: Angeben von HTTP-Header

Um HTTP-Header festzulegen, verwenden die `Headers` Eigenschaft für das Verbindungsobjekt. Das folgende Beispiel zeigt, wie Sie einen HTTP-Header hinzufügen.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a>Gewusst wie: Geben Sie Clientzertifikate

Verwenden Sie zum Hinzufügen von Clientzertifikaten die `AddClientCertificate` Methode für das Verbindungsobjekt.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a>Vorgehensweise: Erstellen Sie die Hubproxy-Klasse

Um Methoden auf dem Client definieren, die ein Hub vom Server aufrufen können, und zum Aufrufen von Methoden für einen Hub auf dem Server, erstellen Sie einen Proxy für den Hub durch Aufrufen von `CreateHubProxy` für das Verbindungsobjekt. Die Zeichenfolge übergebenen `CreateHubProxy` ist der Name der Hub-Klasse, oder den Namen trägt die `HubName` Attribut, wenn auf dem Server verwendet wurde. Die namenszuordnung wird Groß-/Kleinschreibung.

**Hub-Klasse auf server**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

**Erstellen des Clientproxys für Hub-Klasse**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

Wenn Sie ergänzen, um Ihre hubklasse mit einem `HubName` -Attributs festzulegen, verwenden Sie diesen Namen.

**Hub-Klasse auf server**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample12.cs)]

**Erstellen des Clientproxys für Hub-Klasse**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

Wenn Sie aufrufen `HubConnection.CreateHubProxy` mehrmals mit dem gleichen `hubName`, erhalten Sie dieselbe zwischengespeichert `IHubProxy` Objekt.

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können

Um eine Methode definieren, die der Server aufrufen können, verwenden Sie den Proxy des `On` Methode, um einen Ereignishandler zu registrieren.

Methode-namenszuordnung wird Groß-/Kleinschreibung. Z. B. `Clients.All.UpdateStockPrice` auf dem Server führt `updateStockPrice`, `updatestockprice`, oder `UpdateStockPrice` auf dem Client.

Verschiedene Clientplattformen haben unterschiedliche Anforderungen an wie Sie den Code schreiben, die Benutzeroberfläche aktualisieren. Die dargestellten Beispiele gelten für WinRT (Windows Store-.NET)-Clients. WPF, Silverlight und Beispiele für Konsole finden Sie unter [einem separaten Abschnitt weiter unten in diesem Thema](#wpfsl).

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a>Methoden ohne Parameter

Wenn die Methode Sie verarbeiten nicht über Parameter verfügt, verwenden Sie die nicht generische Überladung von der `On` Methode:

**Clientmethode ohne Parameter aufrufen Servercode**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

**WinRT-Clientcode für die Methode, die vom Server ohne Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Methoden mit Parametern, die Parametertypen angeben

Wenn die Methode, die Sie verarbeiten Parameter verfügt, geben Sie die Typen der Parameter als die generischen Typen von der `On` Methode. Es gibt generische Überladung von der `On` Methode, um Ihnen die Angabe von bis zu 8 Parameter (4 unter Windows Phone 7) zu aktivieren. Im folgenden Beispiel wird ein Parameter gesendet, auf die `UpdateStockPrice` Methode.

**Aufrufen der Methode mit einem Parameter Servercode**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

**Die Stock-Klasse für den Parameter verwendet**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample17.cs)]

**WinRT-Clientcode für eine Methode, die vom Server mit einem Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Methoden mit Parametern, dynamische Objekte für die Parameter angeben

Als Alternative zum Angeben von Parametern als generische Typen, die von der `On` -Methode, Sie können Parameter angeben, als dynamische Objekte:

**Aufrufen der Methode mit einem Parameter Servercode**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

**Die Stock-Klasse für den Parameter verwendet**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample20.cs)]

**WinRT-Clientcode für eine Methode, die vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den Parameter aufgerufen ([finden Sie in WPF und Silverlight-Beispielen weiter unten in diesem Thema](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a>Vorgehensweise beim Entfernen eines Handlers

Um einen Handler zu entfernen, rufen Sie die `Dispose` Methode.

**Clientcode für eine vom Server aufgerufene Methode**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

**Client-Code, um den Ereignishandler zu entfernen**

[!code-css[Main](hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Gewusst wie: Servermethoden vom Client aufrufen.

Verwenden Sie zum Aufrufen einer Methode auf dem Server die `Invoke` Methode für die Hubproxy-Klasse.

Verfügt die Server-Methode keinen Wert zurückgibt, verwenden Sie die nicht generische Überladung von der `Invoke` Methode.

**Servercode für eine Methode, die keinen zurückgibt Wert**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

**Client-Code Aufrufen einer Methode, die keinen zurückgibt Wert**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

Wenn die Server-Methode einen Rückgabewert verfügt, geben Sie den Rückgabetyp als den generischen Typ, der die `Invoke` Methode.

**Servercode für eine Methode, die hat einen Rückgabewert und nimmt einen Parameter des komplexen Typs**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

**Die Stock-Klasse, die für die Parameter und Rückgabewert verwendet**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample27.cs)]

**Client-Code Aufrufen einer Methode, die einen Rückgabewert und nimmt einen Parameter des komplexen Typs, in einer ASP.NET 4.5-Async-Methode**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

**Aufrufen einer Methode, die einen Rückgabewert und eine synchrone Methode nimmt einen Parameter des komplexen Typs, der Client-code**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

Die `Invoke` Methode asynchron ausgeführt wird, und gibt eine `Task` Objekt. Wenn Sie keinen `await` oder `.Wait()`, die nächste Codezeile ausgeführt, bevor die Methode, die Sie aufrufen beendet wurde.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung

SignalR bietet die folgenden Verbindung, die Objektlebensdauer-Ereignisse, die Sie behandeln können:

- `Received`: Wird ausgelöst, wenn alle Daten für die Verbindung empfangen werden. Stellt die empfangenen Daten bereit.
- `ConnectionSlow`: Wird ausgelöst, wenn der Client eine Verbindung langsame ist oder häufig löschen erkennt.
- `Reconnecting`: Wird ausgelöst, wenn des zugrunde liegenden Transports, erneut eine Verbindung herzustellen beginnt.
- `Reconnected`: Wird ausgelöst, wenn der zugrunde liegenden Transport die Verbindung wiederhergestellt wurde.
- `StateChanged`: Wird ausgelöst, wenn der Verbindungsstatus ändert. Enthält der alte Status und den neuen Zustand. Informationen zur Verbindung finden Sie unter Statuswerte [ConnectionState-Enumeration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).
- `Closed`: Wird ausgelöst, wenn die Verbindung getrennt wurde.

Beispielsweise sollten Sie Warnmeldungen für Fehler angezeigt, die nicht schwerwiegend sind, aber dazu führen, dass zeitweilige Verbindungsprobleme, wie die Langsamkeit oder häufige Löschen der Verbindung, behandeln die `ConnectionSlow` Ereignis.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample30.cs)]

Weitere Informationen finden Sie unter [verstehen und Behandeln von Ereignissen für Lebensdauer in SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Gewusst wie: Behandeln von Fehlern

Wenn Sie detaillierte Fehlermeldungen auf dem Server nicht explizit aktivieren, enthält das Ausnahmeobjekt an, dem SignalR nach einem Fehler zurückgibt minimale Informationen über den Fehler an. Beispielsweise wenn ein Aufruf von `newContosoChatMessage` ein Fehler auftritt, der die Fehlermeldung in das Fehlerobjekt enthält "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Senden detaillierte Fehlermeldungen für Clients in einer produktionsumgebung wird nicht empfohlen aus Sicherheitsgründen aber wenn Sie möchten detaillierte Fehlermeldungen für aktivieren Problembehandlung, verwenden Sie den folgenden Code auf dem Server.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

Zur Behandlung von Fehlern, die SignalR auslöst, können Sie hinzufügen, einen Handler für die `Error` Ereignis für das Verbindungsobjekt.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample32.cs)]

Zur Behandlung von Fehlern von Methodenaufrufen, umschließen Sie den Code in einem Try / Catch-Block.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Gewusst wie: Aktivieren der clientseitigen Protokollierung

Um die clientseitige Protokollierung zu aktivieren, legen die `TraceLevel` und `TraceWriter` Eigenschaften für das Verbindungsobjekt.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a>WPF, Silverlight und Konsolenanwendung Codebeispiele für Clientmethoden, die der Server aufgerufen werden können

Die Codebeispiele, die zuvor gezeigte zum Definieren von Clientmethoden, die der Server aufrufen kann, gelten für WinRT-Clients. Den folgenden Beispielen wird den entsprechenden Code für WPF, Silverlight und Console Application-Clients.

### <a name="methods-without-parameters"></a>Methoden ohne Parameter

**WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen.**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

**Silverlight-Client-Code für die Methode wird vom Server ohne Parameter aufgerufen.**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

**Console Application-Clientcode für die Methode aufgerufen wird, vom Server ohne Parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Methoden mit Parametern, die Parametertypen angeben

**WPF-Client-Code für eine Methode aufgerufen wird, vom Server mit einem parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

**Silverlight-Client-Code für eine Methode aufgerufen wird, vom Server mit einem parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

**Console Application-Clientcode für eine Methode aufgerufen wird, vom Server mit einem parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Methoden mit Parametern, dynamische Objekte für die Parameter angeben

**WPF-Client-Code für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

**Silverlight-Client-Code für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

**Console Application-Clientcode für eine Methode aufgerufen wird, vom Server mit einem Parameter, die ein dynamisches Objekt verwenden, für den parameter**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]
