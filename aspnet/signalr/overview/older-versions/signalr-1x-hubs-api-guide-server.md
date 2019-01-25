---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
title: ASP.NET SignalR-Hubs-API-Guide - Server (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Dieses Dokument enthält eine Einführung in die Programmierung von der Serverseite die ASP.NET SignalR-Hubs-API für SignalR-Version 1.1, mit Code-Beispiele Demonstratin...
ms.author: bradyg
ms.date: 04/17/2013
ms.assetid: 03e4b9f5-0fea-4d94-959f-014b2762a301
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: 8d544e81f87998581afb2a1228233b4d374ad70a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837389"
---
<a name="aspnet-signalr-hubs-api-guide---server-signalr-1x"></a>ASP.NET SignalR-Hubs-API-Guide - Server (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument enthält eine Einführung in die Programmierung der serverbasierten Aspekte von ASP.NET SignalR-Hubs-API für SignalR-Version 1.1 mit Codebeispielen veranschaulicht allgemeine Optionen.
> 
> Der SignalR-Hubs-API können Sie die Remoteprozeduraufrufe (RPCs) von einem Server verbundene Clients und von den Clients an den Server vornehmen. Im Server-Code Sie Methoden definieren, die von Clients aufgerufen werden können, und rufen Sie Methoden, die auf dem Client ausgeführt. Im Clientcode Sie Methoden definieren, die vom Server aufgerufen werden können, und rufen Sie Methoden, die auf dem Server ausgeführt. SignalR ist für alle Client-zu-Server sich für Sie übernimmt.
> 
> SignalR bietet außerdem eine Low-Level-API wird aufgerufen, dauerhafte Verbindungen. Eine Einführung in die SignalR-Hubs und dauerhafte Verbindungen oder für ein Lernprogramm, das zeigt, wie Sie eine vollständige SignalR-Anwendung erstellen, finden Sie unter [SignalR - erste Schritte](index.md).


## <a name="overview"></a>Übersicht

Dieses Dokument enthält folgende Abschnitte:

- [So registrieren die SignalR-Route und SignalR-Optionen konfigurieren](#route)

    - [Die /signalr-URL](#signalrurl)
    - [Konfigurieren von SignalR-Optionen](#options)
- [Das Erstellen und verwenden die Hub-Klassen](#hubclass)

    - [Hub-Objektlebensdauer](#transience)
    - [Camel-Schreibweise des Hub-Namen in der JavaScript-clients](#hubnames)
    - [Mehrere Hubs](#multiplehubs)
- [Wie Sie Methoden in der hubklasse zu definieren, die Clients aufrufen können](#hubmethods)

    - [Camel-Schreibweise von Methodennamen in JavaScript-clients](#methodnames)
    - [Beim asynchron ausführen.](#asyncmethods)
    - [Definieren von Überladungen](#overloads)
- [Das Client Aufrufen von Methoden aus der hubklasse](#callfromhub)

    - [Wählen die Clients erhalten die RPC](#selectingclients)
    - [Keine Überprüfung während der Kompilierung Methodennamen](#dynamicmethodnames)
    - [Übereinstimmung von Groß-/Kleinschreibung-Methode](#caseinsensitive)
    - [Asynchrone Ausführung](#asyncclient)
- [Gewusst wie: Verwalten der Gruppenmitgliedschaft von Hub-Klasse](#groupsfromhub)

    - [Asynchrone Ausführung der Add- und Remove-Methoden](#asyncgroupmethods)
    - [Group-Mitgliedschaft-Persistenz](#grouppersistence)
    - [Einzelbenutzer-Gruppen](#singleusergroups)
- [Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse in der hubklasse Verbindung](#connectionlifetime)

    - [Wenn OnConnected, OnDisconnected und OnReconnected aufgerufen werden](#onreconnected)
    - [Aufruferstatus nicht aufgefüllt](#nocallerstate)
- [Gewusst wie: Abrufen von Informationen über den Client aus der Kontexteigenschaft](#contextproperty)
- [Gewusst wie: Status zwischen Clients und Hub-Klasse übergeben.](#passstate)
- [Gewusst wie: Behandeln von Fehlern in der hubklasse](#handleErrors)
- [Das Client-Methoden aufrufen und Verwalten von Gruppen von außerhalb der Hub-Klasse](#callfromoutsidehub)

    - [Aufrufen von Clientmethoden](#callingclientsoutsidehub)
    - [Verwalten der Gruppenmitgliedschaft](#managinggroupsoutsidehub)
- [Gewusst wie: Aktivieren der Ablaufverfolgung](#tracing)
- [Gewusst wie: Anpassen die Pipeline Hubs](#hubpipeline)

Dokumentation für das Programm-Clients finden Sie in den folgenden Ressourcen:

- [SignalR-Hubs-API-Leitfaden – JavaScript-Client](index.md)
- [Leitfaden für SignalR Hubs-API – .NET-Client](index.md)

Links zu Themen,-API-Referenz sind, .NET 4.5-Version der API. Wenn Sie .NET 4 verwenden, finden Sie unter [.NET 4-Version der API-Themen](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="route"></a>

## <a name="how-to-register-the-signalr-route-and-configure-signalr-options"></a>So registrieren die SignalR-Route und SignalR-Optionen konfigurieren

Um die Route definieren, die Clients verwenden, um mit Ihrem Hub herstellen, rufen Sie die [MapHubs](https://msdn.microsoft.com/library/system.web.routing.signalrrouteextensions.maphubs(v=vs.111).aspx) Methode, wenn die Anwendung gestartet wird. `MapHubs` ist ein [Erweiterungsmethode](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) für die `System.Web.Routing.RouteCollection` Klasse. Das folgende Beispiel zeigt, wie Sie definieren die SignalR-Hubs-Route in der *"Global.asax"* Datei.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample1.cs)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample2.cs?highlight=5)]

Wenn Sie SignalR-Funktionen zu einer ASP.NET MVC-Anwendung hinzufügen, stellen Sie sicher, dass die SignalR-Route vor anderen Routen hinzugefügt wird. Weitere Informationen finden Sie unter [Tutorial: Erste Schritte mit SignalR und MVC 4](index.md).

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a>Die /signalr-URL

Standardmäßig ist die Routen-URL, die Clients verwenden, um mit Ihrem Hub herstellen "/ Signalr". (Verwechseln Sie nicht diese URL mit der URL "/ Signalr/Hubs" für die automatisch generierte JavaScript-Datei handelt. Weitere Informationen zu den generierten Proxy, finden Sie unter [für die SignalR-Hubs-API – JavaScript-Client – den generierten Proxy und was dies für Sie übernimmt](index.md).)

Gibt es möglicherweise außergewöhnliche Umständen, die diese Basis-URL für SignalR nicht verwendbar zu machen; Angenommen, Sie verfügen über einen Ordner in Ihrem Projekt mit dem Namen *Signalr* und nicht den Namen ändern möchten. In diesem Fall können Sie die base-URL ändern, wie in den folgenden Beispielen gezeigt (ersetzen Sie "/ Signalr" im Beispielcode mit der gewünschten URL).

**Server-Code, der die URL angibt.**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample3.cs?highlight=1)]

**JavaScript-Clientcode, der angibt, die URL (mit den generierten Proxy)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample4.js?highlight=1)]

**JavaScript-Clientcode, der angibt, die URL (ohne den generierten Proxy)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample5.js?highlight=1)]

**.NET Client-Code, der angibt, die URL**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample6.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a>Konfigurieren von SignalR-Optionen

Der Überladungen der `MapHubs` Methode ermöglichen es Ihnen, eine benutzerdefinierte URL, einen benutzerdefinierten Abhängigkeitskonfliktlöser und die folgenden Optionen angeben:

- Domänenübergreifende Aufrufe aus Browser-Clients zu aktivieren.

    In der Regel, wenn der Browser eine Seite lädt `http://contoso.com`, die SignalR-Verbindung ist auf der gleichen Domäne `http://contoso.com/signalr`. Wenn die Seite `http://contoso.com` stellt eine Verbindung mit `http://fabrikam.com/signalr`, d. h. eine domänenübergreifende-Verbindung. Aus Sicherheitsgründen sind die domänenübergreifende Verbindungen standardmäßig deaktiviert. Weitere Informationen finden Sie unter [für die ASP.NET SignalR-Hubs-API – JavaScript-Client – Gewusst wie: Herstellen einer Verbindung domänenübergreifende](index.md).
- Detaillierte Fehlermeldungen zu aktivieren.

    Wenn Fehler auftreten, werden das Standardverhalten von SignalR ab, an Clients senden eine Benachrichtigung ohne Details, was passiert ist. Ausführliche Fehlerinformationen an Clients gesendet wird nicht in der Produktion empfohlen, weil böswillige Benutzer, die Informationen in die Angriffe auf Ihre Anwendung verwenden werden können. Informationen zur Problembehandlung können Sie diese Option verwenden, um vorübergehend informativere Fehlerberichterstattung zu aktivieren.
- Deaktivieren Sie die automatisch generierte JavaScript-Proxy-Dateien.

    Standardmäßig wird eine JavaScript-Datei mit Proxys für den Hub-Klassen als Reaktion auf die URL "/ Signalr/Hubs" generiert. Wenn Sie nicht die JavaScript-Proxys verwenden möchten oder wenn Sie diese Datei manuell zu generieren, und klicken Sie auf einer physischen Datei in Ihre Clients verweisen möchten, können Sie diese Option, um Proxygenerierung zu deaktivieren. Weitere Informationen finden Sie unter [für die SignalR-Hubs-API - JavaScript-Client – erstellen eine physische Datei für die SignalR generierter Proxy](index.md).

Das folgende Beispiel zeigt, wie in einem Aufruf der SignalR-Verbindungs-URL und diese Optionen geben die `MapHubs` Methode. Um eine benutzerdefinierte URL anzugeben, ersetzen Sie "/ Signalr" im Beispiel durch die URL, die Sie verwenden möchten.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample7.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a>Das Erstellen und verwenden die Hub-Klassen

Um einen Hub erstellen möchten, erstellen Sie eine abgeleitete Klasse [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx). Das folgende Beispiel zeigt eine einfache hubklasse für eine Chat-Anwendung.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample8.cs)]

In diesem Beispiel ein verbundener Client aufrufen kann die `NewContosoChatMessage` -Methode, und wenn dies der Fall, werden die empfangenen Daten mithilfe von an alle verbundenen Clients.

<a id="transience"></a>

### <a name="hub-object-lifetime"></a>Hub-Objektlebensdauer

Sie nicht die Hub-Klasse instanziieren oder seine Methoden aufrufen, aus Ihrem eigenen Code auf dem Server; All dies wird durch die Pipeline für SignalR-Hubs für Sie erledigt. SignalR erstellt eine neue Instanz der Klasse Hub jedes Mal, es muss eine Hub-Vorgängen, z. B. wenn ein Client eine Verbindung herstellt, trennt die Verbindung, oder stellt einen Methodenaufruf an dem Server zu behandeln.

Da die Instanzen der Hub-Klasse nur vorübergehend auftreten, können nicht Sie sie zur Beibehaltung des Zustands in einem Methodenaufruf an den nächsten verwenden. Jedes Mal erhält der Server einem Methodenaufruf von einem Client, eine neue Instanz von Prozessen Ihr Hub die Nachricht. Um Status über mehrere Verbindungen und Methodenaufrufe zu gewährleisten, verwenden Sie eine andere Methode z. B. eine Datenbank oder eine statische Variable auf die hubklasse oder eine andere Klasse, die nicht von abgeleitet ist `Hub`. Wenn Sie Daten im Arbeitsspeicher beibehalten, werden mit einer Methode wie z. B. eine statische Variable auf den Hub-Klasse, die Daten verloren, wenn die Anwendungsdomäne wiederverwendet wird.

Wenn Sie möchten die Nachrichten für Clients über Ihren eigenen Code zu senden, die außerhalb der hubklasse ausgeführt wird, dies nicht möglich, durch eine Instanz des Hub-Klasse instanziieren, aber können Sie dies tun, indem Sie einen Verweis auf das Kontextobjekt für die SignalR für die Hub-Klasse abrufen. Weitere Informationen finden Sie unter [wie Client-Methoden aufrufen und Verwalten von Gruppen von außerhalb der hubklasse](#callfromoutsidehub) weiter unten in diesem Thema.

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a>Camel-Schreibweise des Hub-Namen in der JavaScript-clients

Standardmäßig beziehen sich JavaScript-Clients für Hubs mit einer Version in Kamel-Schreibweise des Klassennamens. SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann. Im vorherige Beispiel würde, die als bezeichnet `contosoChatHub` im JavaScript-Code.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample9.cs?highlight=1)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample10.js?highlight=1)]

Wenn Sie einen anderen Namen für die Clients verwenden, fügen Sie möchten die `HubName` Attribut. Bei Verwendung einer `HubName` Attribut, gibt es keine Änderung in Camel-Case für JavaScript-Clients.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample11.cs?highlight=1)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample12.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a>Mehrere Hubs

Sie können mehrere Hub-Klassen in einer Anwendung definieren. Wenn Sie dies tun, wird die Verbindung freigegeben, aber Gruppen sind getrennt:

- Alle Clients verwenden die gleiche URL zum Herstellen einer SignalR-Verbindung mit Ihrem Dienst ("/ Signalr" oder die benutzerdefinierte URL, wenn Sie angegeben), und dass die Verbindung für alle Hubs verwendet wird, die vom Dienst definiert sind.

    Es gibt keine Leistungsunterschiede für mehrere Hubs im Vergleich zu allen Hub-Funktionalität in einer einzelnen Klasse definieren.
- Alle Hubs erhalten die gleiche Informationen des HTTP-Anforderung.

    Da alle Hubs die gleiche Verbindung gemeinsam nutzen, ist die einzige HTTP-Anforderungsinformationen, die der Server ruft was in der ursprünglichen HTTP-Anforderung stammt, die den SignalR-Verbindung hergestellt wird. Wenn Sie die verbindungsanforderung verwenden, um Informationen vom Client an den Server zu übergeben, indem Sie eine Abfragezeichenfolge angeben, können nicht Sie unterschiedlichen Abfragezeichenfolgen für unterschiedliche Hubs bereitstellen. Alle Hubs erhalten die gleiche Informationen.
- Die generierte JavaScript-Proxys-Datei enthält die Proxys für alle Hubs in einer Datei.

    Weitere Informationen zu JavaScript-Proxys, finden Sie unter [für die SignalR-Hubs-API – JavaScript-Client – den generierten Proxy und was dies für Sie übernimmt](index.md).
- Gruppen werden in den Hubs definiert.

    In SignalR, die Sie definieren, können mit dem Namen Gruppen mit Teilmengen von verbundenen Clients zu senden. Gruppen werden separat für jeden Hub verwaltet. Beispielsweise würde eine Gruppe namens "Administratoren" enthalten einen Satz von Clients für Ihre `ContosoChatHub` Klasse und demselben Gruppennamen würden, finden Sie in einen anderen Satz von Clients für Ihre `StockTickerHub` Klasse.

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a>Wie Sie Methoden in der hubklasse zu definieren, die Clients aufrufen können

Deklarieren Sie eine öffentliche Methode, um eine Methode auf dem Hub verfügbar gemacht werden, die vom Client aufgerufen werden sollen, wie in den folgenden Beispielen gezeigt.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample14.cs?highlight=3)]

Sie können einen Rückgabetyp und Parameter, einschließlich von komplexen Typen und Arrays, wie in jeder C#-Methode angeben. Alle Daten, die Sie in den Parametern erhalten oder an den Aufrufer zurückgeben werden zwischen dem Client und Server übermittelt, mithilfe von JSON und SignalR behandelt automatisch die Bindung von komplexen Objekten und Arrays von Objekten.

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a>Camel-Schreibweise von Methodennamen in JavaScript-clients

Standardmäßig beziehen sich JavaScript-Clients auf Hub-Methoden mit einer Version in Kamel-Schreibweise des Methodennamens. SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample15.cs?highlight=1)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample16.js?highlight=1)]

Wenn Sie einen anderen Namen für die Clients verwenden, fügen Sie möchten die `HubMethodName` Attribut.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample17.cs?highlight=1)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a>Beim asynchron ausführen.

Wenn die Methode wird werden lang andauernde oder Aufgaben über würde warten, z. B. einer Datenbanksuche oder einen Webdienstaufruf umfassen, die Hub-Methode asynchron machen, indem Sie zurückgeben einer [Aufgabe](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (anstelle von `void` zurückgegeben) oder [ Aufgabe&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) Objekt (anstelle von `T` Rückgabetyp). Bei der Rückkehr eine `Task` Objekt aus der SignalR-Methode wartet darauf, dass die `Task` abgeschlossen werden, und er sendet dann das Ergebnis entpackte zurück an den Client, es gibt also keinen Unterschied in der code wie den Aufruf der Methode auf dem Client.

Vornehmen einer hubmethode vermeidet asynchrone blockiert die Verbindung, wenn den WebSocket-Transport verwendet. Wenn eine hubmethode synchron ausgeführt, und der Transport WebSocket ist, werden nachfolgende Aufrufe von Methoden auf dem Hub vom gleichen Client blockiert, bis die Hub-Methode abgeschlossen ist.

Das folgende Beispiel zeigt die gleiche Methode programmiert, dass Sie synchron ausgeführt, oder asynchron, gefolgt von JavaScript-Clientcode, der zum Aufrufen der beiden Versionen funktioniert.

**Synchrone**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample19.cs)]

**Asynchrone - ASP.NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample21.js)]

Weitere Informationen zur Verwendung von asynchronen Methoden in ASP.NET 4.5 finden Sie unter [mithilfe von asynchronen Methoden in ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).

<a id="overloads"></a>

### <a name="defining-overloads"></a>Definieren von Überladungen

Wenn Sie Überladungen für eine Methode definieren möchten, muss die Anzahl von Parametern in jede Überladung sich unterscheiden. Wenn Sie eine Überladung, indem er verschiedene Parametertypen einfach unterscheiden, die Hub-Klasse wird kompiliert, aber der SignalR-Dienst löst eine Ausnahme zur Laufzeit, wenn Clients versuchen, rufen Sie eine der Überladungen.

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a>Das Client Aufrufen von Methoden aus der hubklasse

Um den Client vom Server Methoden aufrufen, verwenden Sie die `Clients` Eigenschaft in einer Methode in der Hub-Klasse. Das folgende Beispiel zeigt die Servercode, der Aufrufe `addNewMessageToPage` auf allen verbundenen Clients, und Clientcode, der die Methode in einem JavaScript-Client definiert.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample22.cs?highlight=5)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample23.html?highlight=1)]

Der Rückgabewert kann nicht aus einer Clientmethode abgerufen werden; Diese Syntax sind `int x = Clients.All.add(1,1)` funktioniert nicht.

Sie können komplexe Typen und Arrays für die Parameter angeben. Das folgende Beispiel übergibt einen komplexen Typ in einen Methodenparameter an dem Client.

**Servercode, der eine Clientmethode, die mithilfe von eines komplexen Objekts aufruft**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample24.cs?highlight=3)]

**Server-Code, die das komplexe Objekt definiert.**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample25.cs?highlight=1)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample26.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a>Wählen die Clients erhalten die RPC

Gibt die Clients-Eigenschaft einer [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) -Objekt, das bietet mehrere Optionen zum angeben, welche Clients die RPC erhält:

- Alle verbundenen Clients.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample27.cs)]
- Nur der aufrufende Client.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample28.cs)]
- Alle Clients mit Ausnahme des aufrufenden Clients.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample29.cs)]
- Einen bestimmten Client identifiziert, die vom Verbindungs-ID.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample30.css)]

    Dieses Beispiel ruft `addContosoChatMessageToPage` für den aufrufenden Client und hat dieselbe Wirkung wie die Verwendung von `Clients.Caller`.
- Alle verbundenen Clients mit Ausnahme der angegebenen Clients identifizierte Verbindungs-ID.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample31.cs)]
- Alle verbundenen Clients in einer angegebenen Gruppe.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample32.css)]
- Alle verbundenen Clients in einer angegebenen Gruppe mit Ausnahme der angegebenen Clients identifizierte Verbindungs-ID.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample33.cs)]
- Alle verbundenen Clients in einer angegebenen Gruppe mit Ausnahme des aufrufenden Clients.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample34.css)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a>Keine Überprüfung während der Kompilierung Methodennamen

Der Name der Methode, die Sie angeben wird als ein dynamisches Objekt interpretiert, was bedeutet, dass es gibt keine IntelliSense oder der Kompilierzeit-Überprüfung für sie. Der Ausdruck wird zur Laufzeit ausgewertet. Wenn der Methodenaufruf ausgeführt wird, werden SignalR den Methodennamen und die Parameterwerte an den Client sendet, und wenn der Client eine Methode verfügt, die mit dem Namen übereinstimmt, dass die Methode aufgerufen wird und die Werte der Parameter an es übergeben. Wenn keine übereinstimmende Methode auf dem Client gefunden wird, wird kein Fehler ausgelöst. Weitere Informationen zum Format der Daten, die SignalR an den Client hinter den Kulissen überträgt, wenn Sie eine Clientmethode aufrufen, finden Sie unter [Einführung zu SignalR](index.md).

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a>Übereinstimmung von Groß-/Kleinschreibung-Methode

Methode-namenszuordnung wird Groß-/Kleinschreibung. Z. B. `Clients.All.addContosoChatMessageToPage` auf dem Server führt `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, oder `addContosoChatMessageToPage` auf dem Client.

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a>Asynchrone Ausführung

Die Methode, die Sie aufrufen, führt asynchron aus. Jeder Code, der nach ein Methodenaufruf an einen Client ohne Wartezeiten für SignalR, um den Vorgang abzuschließen, Daten an Clients übertragen, es sei denn, die Sie angeben, dass die nachfolgenden Codezeilen, auf den Abschluss der Methode warten soll sofort ausgeführt werden. Die folgenden Codebeispiele zeigen, wie Clientmethoden sequenziell ausführen können, eine mithilfe von code in .NET 4.5, funktioniert und eins mit diesem in .NET 4 funktioniert code.

**.NET 4.5-Beispiel**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample35.cs?highlight=1,3)]

**Beispiel für .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample36.cs?highlight=3-4)]

Bei Verwendung von `await` oder `ContinueWith` um warten, bis eine Clientmethode abgeschlossen ist, bevor die nächste Codezeile ausgeführt wird, dies bedeutet nicht, dass Clients die Nachricht tatsächlich empfangen werden, bevor die nächste Codezeile ausgeführt wird. "Abschluss", der einen Methodenaufruf für den Client bedeutet lediglich, dass SignalR alles, was Sie zum Senden der Nachricht abgeschlossen hat. Wenn Sie die Überprüfung benötigen, dass Clients die Meldung empfangen, müssen Sie diesen Mechanismus selbst programmieren. Sie können z. B. code eine `MessageReceived` -Methode für den Hub, und klicken Sie in der `addContosoChatMessageToPage` Methode auf dem Client, Sie rufen `MessageReceived` danach, was auch immer Sie arbeiten, müssen für die Sie auf dem Client. In `MessageReceived` im Hub möglich tatsächlichen Client Empfang und Verarbeitung der ursprüngliche Methodenaufruf unabhängig arbeiten abhängt.

### <a name="how-to-use-a-string-variable-as-the-method-name"></a>Wie Sie eine String-Variable als Name der Methode zu verwenden

Wenn Sie eine Clientmethode aufrufen, mit eine String-Variable als dem Methodennamen, umwandeln möchten `Clients.All` (oder `Clients.Others`, `Clients.Caller`usw.), `IClientProxy` und rufen Sie dann [Invoke (MethodName, args…) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample37.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a>Gewusst wie: Verwalten der Gruppenmitgliedschaft von Hub-Klasse

Gruppen in SignalR bieten eine Methode zum Übertragen von Nachrichten an die angegebene Teilmengen von verbundenen Clients. Eine Gruppe kann eine beliebige Anzahl Clients umfassen, und ein Client kann Mitglied einer beliebigen Anzahl von Gruppen sein.

Verwenden Sie zum Verwalten der Gruppenmitgliedschaft die [hinzufügen](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) und [entfernen](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) von bereitgestellten Methoden die `Groups` Eigenschaft der Hub-Klasse. Das folgende Beispiel zeigt die `Groups.Add` und `Groups.Remove` Methoden im Hub-Methoden, die von Client-Code aufgerufen werden, gefolgt von JavaScript-Clientcode, der sie aufgerufen.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample38.cs?highlight=5,10)]

**JavaScript-Client mithilfe des generierten Proxys**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample39.js)]

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample40.js)]

Sie haben keine Gruppen explizit zu erstellen. In Kraft ist eine Gruppe automatisch beim ersten Sie den Namen in einem Aufruf geben erstellt `Groups.Add`, und beim Entfernen der letzten Verbindungs aus der Mitgliedschaft in der er gelöscht.

Es ist keine API zum Abrufen der Mitgliederliste einer Gruppe oder eine Liste der Gruppen ein. SignalR sendet Nachrichten an Clients und-Gruppen auf Grundlage einer [Pub/Sub-Modells](http://en.wikipedia.org/wiki/Publish/subscribe), und der Server behält keine Listen mit Gruppen oder Gruppenmitgliedschaften. Dadurch wird das Maximieren der Skalierbarkeit, da Wenn Sie einen Knoten zu einer Webfarm hinzufügen, verfügt über einem beliebigen Zustand, in der SignalR verwaltet, die auf den neuen Knoten weitergegeben werden.

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a>Asynchrone Ausführung der Add- und Remove-Methoden

Die `Groups.Add` und `Groups.Remove` Methoden asynchron ausgeführt. Wenn Sie einen Client zu einer Gruppe hinzufügen und eine Nachricht sofort an den Client senden, mit dem Group möchten, müssen Sie sicherstellen, dass die `Groups.Add` Methode zuerst abgeschlossen ist. Die folgenden Codebeispiele zeigen, wie diese mithilfe von Code, der funktioniert in .NET 4.5 und mithilfe von Code, der in .NET 4 funktioniert

**.NET 4.5-Beispiel**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

**Beispiel für .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample42.cs?highlight=3-4)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a>Group-Mitgliedschaft-Persistenz

SignalR verfolgt Verbindungen, nicht von Benutzern, wenn also einen Benutzer in der gleichen Gruppe werden jedes Mal, wenn der Benutzer wird eine Verbindung soll, muss aufgerufen werden `Groups.Add` jedes Mal, wenn der Benutzer eine neue Verbindung hergestellt wird.

Nach einem vorübergehenden Trennung der Verbindung kann manchmal SignalR die Verbindung automatisch wiederherstellen. In diesem Fall SignalR ist die gleiche Verbindung wiederherstellen nicht Herstellen einer neuen Verbindung, und daher des Clients der Gruppenmitgliedschaft wird automatisch wiederhergestellt. Dies ist möglich, auch, wenn die temporären Unterbrechung einen Neustart des Servers oder einen Fehler, das Ergebnis ist da Verbindungsstatus für jeden Client, einschließlich der Gruppenmitgliedschaften, Roundtrip an den Client ausgeführt wird. Wenn ein Server ausfällt und durch einen neuen Server ersetzt wird, bevor die Verbindung ein eintritt Timeout, kann ein Client erneut automatisch eine Verbindung mit dem neuen Server herstellen und erneut registrieren Sie sich für Gruppen, denen er Mitglied ist.

Wenn eine Verbindung nach einer verbindungsunterbrechung, nicht automatisch wiederhergestellt werden kann oder wenn die Verbindung ein auftritt Timeout oder wenn der Client die Verbindung trennt (z. B. bei ein Browser zu einer neuen Seite navigiert), werden die Gruppenmitgliedschaften verloren gehen. Das nächste Mal, die der Benutzer eine Verbindung herstellt, werden eine neue Verbindung. Um Gruppenmitgliedschaften zu gewährleisten, wenn derselbe Benutzer eine neue Verbindung herstellt, verfügt Ihre Anwendung zum Nachverfolgen von Zuordnungen zwischen Benutzern und Gruppen und Wiederherstellen von Gruppenmitgliedschaften jedes Mal ein Benutzer eine neue Verbindung hergestellt wird.

Weitere Informationen zu Verbindungen und erneute Verbindungen finden Sie unter [Verbindung Objektlebensdauer-Ereignisse im Hub-Klasse behandeln](#connectionlifetime) weiter unten in diesem Thema.

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a>Einzelbenutzer-Gruppen

Anwendungen, die in der Regel verwenden Sie SignalR können zum Nachverfolgen der Zuordnungen zwischen Benutzern und Verbindungen, um zu ermitteln, welche Benutzer eine Nachricht gesendet hat und welche Benutzer eine Nachricht empfangen sollte. Gruppen werden in einem der zwei häufig verwendete Muster dafür verwendet.

- Gruppen für einzelne Benutzer.

    Sie können geben den Benutzernamen an, wie dem Gruppennamen und die aktuelle Verbindungs-ID zur Gruppe hinzufügen, jedes Mal, wenn der Benutzer eine Verbindung herstellt oder erneut eine Verbindung herstellt. Zum Senden von Nachrichten an den Benutzer an der Gruppe gesendet. Ein Nachteil dieser Methode ist, dass die Gruppe keine Ihnen eine Möglichkeit bieten, um herauszufinden, ob der Benutzer online oder offline ist.
- Nachverfolgen von Zuordnungen zwischen den Benutzernamen und Verbindungs-IDs.

    Sie können eine Zuordnung zwischen jeden Benutzernamen und ein oder mehrere Verbindungs-IDs in einem Wörterbuch oder einer Datenbank speichern und aktualisieren Sie die gespeicherten Daten jedes Mal, die der Benutzer eine Verbindung herstellt oder trennt die Verbindung. Zum Senden von Nachrichten an den Benutzer geben Sie den Verbindungs-IDs. Ein Nachteil dieser Methode ist, dass es sich um mehr Arbeitsspeicher verwendet.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a>Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse in der hubklasse Verbindung

Typische Gründe für die Verarbeitung der Verbindung Objektlebensdauer-Ereignisse sind, um nachzuverfolgen, ob ein Benutzer oder nicht verbunden ist, und um zu verfolgen die Zuordnung zwischen den Benutzernamen und Verbindungs-IDs. Um Ihren eigenen Code auszuführen, wenn Clients eine Verbindung herstellen oder trennen, überschreiben die `OnConnected`, `OnDisconnected`, und `OnReconnected` virtuelle Methoden des Hubs-Klasse, wie im folgenden Beispiel gezeigt.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample43.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a>Wenn OnConnected, OnDisconnected und OnReconnected aufgerufen werden

Jedes Mal ein Browser zu einer neuen Seite navigiert eine neue Verbindung muss hergestellt werden, was bedeutet SignalR führt die `OnDisconnected` Methode, gefolgt von der `OnConnected` Methode. SignalR erstellt eine neuen Verbindungs-ID immer, wenn eine neue Verbindung hergestellt wird.

Die `OnReconnected` Methode wird aufgerufen, wenn in Verbindung, die automatisch von SignalR wiederherstellen können, z. B. wenn ein Kabel vorübergehend getrennt und wieder vor dem Timeout der Verbindung eine temporäre Unterbrechung vorliegt. Die `OnDisconnected` Methode wird aufgerufen, wenn der Client getrennt und SignalR kann nicht automatisch erneut eine Verbindung herstellen, z. B. wenn ein Browser zu einer neuen Seite navigiert. Eine mögliche Sequenz von Ereignissen für einen bestimmten Client also `OnConnected`, `OnReconnected`, `OnDisconnected`; oder `OnConnected`, `OnDisconnected`. Die Sequenz wird nicht angezeigt, `OnConnected`, `OnDisconnected`, `OnReconnected` für eine bestimmte Verbindung.

Die `OnDisconnected` Methode nicht in einigen Szenarien, z. B. beim Ausfall eines Servers aufgerufen wird, oder die App-Domäne ruft wiederverwendet. Wenn Sie einen anderen Server wird in Zeile oder die Anwendungsdomäne abgeschlossen ist, die Wiederverwendung, einige Clients möglicherweise erneut eine Verbindung herstellen und Auslösen der `OnReconnected` Ereignis.

Weitere Informationen finden Sie unter [verstehen und Behandeln von Ereignissen für Lebensdauer in SignalR](index.md).

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a>Aufruferstatus nicht aufgefüllt

Die Verbindung für die Lebensdauer Ereignishandlermethoden heißen vom Server ab, dies bedeutet, dass auf einem beliebigen Zustand, die Sie in aufnehmen die `state` Objekt auf dem Client werden nicht aufgefüllt werden, der `Caller` Eigenschaft auf dem Server. Informationen zu den `state` Objekt und die `Caller` -Eigenschaft finden Sie unter [wie Zustand zwischen Clients und Hub-Klasse übergeben](#passstate) weiter unten in diesem Thema.

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a>Gewusst wie: Abrufen von Informationen über den Client aus der Kontexteigenschaft

Rufen Sie Informationen über den Client mit der `Context` Eigenschaft der Hub-Klasse. Die `Context` -Eigenschaft gibt eine [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) Objekt bietet Zugriff auf die folgenden Informationen:

- Der Verbindungs-ID des aufrufenden Clients.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample44.cs?highlight=1)]

    Die Verbindungs-ID ist eine GUID, die von SignalR zugewiesen wird (Sie können nicht den Wert in Ihrem eigenen Code angegeben). Es ist ein Verbindungs-ID für jede Verbindung, und die gleiche Verbindung, die ID von allen Hubs verwendet wird, wenn Sie mehrere Hubs in Ihrer Anwendung haben.
- HTTP-Header der Daten.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample45.cs?highlight=1)]

    Sie können auch HTTP-Header vom abrufen `Context.Headers`. Der Grund für mehrere Verweise auf das gleiche ist, dass `Context.Headers` wurde zuerst erstellt die `Context.Request` Eigenschaft später hinzugefügt wurde und `Context.Headers` wurde aus Kompatibilitätsgründen beibehalten.
- Abfragen von Zeichenfolgendaten.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample46.cs?highlight=1)]

    Sie können auch Daten aus der Abfragezeichenfolge erhalten `Context.QueryString`.

    Die Abfragezeichenfolge, die Sie in dieser Eigenschaft erhalten ist diejenige, die mit der HTTP-Anforderung verwendet wurde, die die SignalR-Verbindung hergestellt. Sie können Abfragezeichenfolgen-Parameter auf dem Client hinzufügen, konfigurieren Sie die Verbindung, die eine bequeme Möglichkeit, Daten über den Client vom Client an den Server übergeben wird. Das folgende Beispiel zeigt eine Möglichkeit zum Hinzufügen einer Abfragezeichenfolge in einem JavaScript-Client, wenn Sie den generierten Proxy verwenden.

    [!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample47.js?highlight=1)]

    Weitere Informationen zu Abfragezeichenfolgen-Parameter festlegen, finden Sie unter der API-Handbüchern für die [JavaScript](index.md) und [.NET](index.md) Clients.

    Sie finden die Transportmethode, die für die Verbindung in den Abfrage-Zeichenfolgendaten, sowie einige andere Werte, die intern von SignalR verwendet verwendet:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample48.cs)]

    Der Wert des `transportMethod` "WebSockets", "ServerSentEvents", "ForeverFrame" oder "LongPolling" werden. Beachten Sie, dass, wenn Sie diesen Wert, in Überprüfen der `OnConnected` -Ereignishandlermethode in einigen Szenarien erhalten Sie möglicherweise zunächst einen Transportwert, der nicht die endgültige ausgehandelten Transportmethode für die Verbindung ist. In diesem Fall wird die Methode löst eine Ausnahme aus, und wird später erneut aufgerufen, wenn die endgültige Transportmethode hergestellt wird.
- Cookies.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    Sie erhalten auch Cookies von `Context.RequestCookies`.
- Benutzerinformationen.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample50.cs?highlight=1)]
- Das HttpContext-Objekt, für die Anforderung:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample51.cs?highlight=1)]

    Verwenden Sie diese Methode anstelle `HttpContext.Current` zum Abrufen der `HttpContext` Objekt für die SignalR-Verbindung.

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a>Gewusst wie: Status zwischen Clients und Hub-Klasse übergeben.

Der Clientproxy stellt eine `state` Objekt in der Sie Daten, die mit jedem Methodenaufruf an den Server übertragen werden sollen speichern können. Auf dem Server können Sie zugreifen, diese Daten in die `Clients.Caller` Eigenschaft im Hub-Methoden, die von Clients aufgerufen werden. Die `Clients.Caller` ist nicht für die Verbindung für die Lebensdauer Ereignishandlermethoden ausgefüllt `OnConnected`, `OnDisconnected`, und `OnReconnected`.

Erstellen oder Aktualisieren von Daten in die `state` Objekt und die `Clients.Caller` Eigenschaft funktioniert in beide Richtungen. Sie können Werte auf dem Server aktualisieren und sie werden zurück an den Client übergeben.

Das folgende Beispiel zeigt die JavaScript-Clientcode, mit dem Zustand für die Übertragung an den Server mit jedem Methodenaufruf von speichert.

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample52.js?highlight=1-2)]

Das folgende Beispiel zeigt den entsprechenden Code in einen .NET Client.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample53.cs?highlight=1-2)]

In der Hub-Klasse, können Sie zugreifen, diese Daten in die `Clients.Caller` Eigenschaft. Das folgende Beispiel zeigt Code, der den Zustand, der im vorherigen Beispiel genannten abruft.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample54.cs?highlight=3-4)]

> [!NOTE]
> Dieser Mechanismus für den persistenten Zustand sollte nicht für große Mengen an Daten, da alles, was Sie in fügen der `state` oder `Clients.Caller` -Eigenschaft ist mit dem jeder Methodenaufruf zurückgeleitet. Es empfiehlt sich für kleinere Elemente wie Benutzernamen oder Leistungsindikatoren.


<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a>Gewusst wie: Behandeln von Fehlern in der hubklasse

Zur Behandlung von Fehlern, die in Ihrem Hub-Klasse, Methoden auftreten, verwenden Sie eine oder beide der folgenden Methoden:

- Umschließen Sie den Methodencode in Try-Catch-Blöcke, und melden Sie sich das Ausnahmeobjekt. Sie können die Ausnahme an den Client senden, zum Debuggen, aber für die Sicherheit der Gründe, senden detaillierte Informationen für Clients in einer produktionsumgebung werden nicht empfohlen.
- Erstellen eines Hubs Pipeline-Moduls, das behandelt die [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) Methode. Das folgende Beispiel zeigt eine Pipeline-Modul, das protokolliert Fehler, gefolgt vom Code in "Global.asax", die das Modul in die Hubs-Pipeline einfügt.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample55.cs)]

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample56.cs?highlight=3)]

Weitere Informationen zu den Hub-Pipeline-Modulen, finden Sie unter [Gewusst wie: Anpassen die Pipeline Hubs](#hubpipeline) weiter unten in diesem Thema.

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a>Gewusst wie: Aktivieren der Ablaufverfolgung

Fügen Sie zum Aktivieren der serverseitigen Ablaufverfolgung ein system.diagnostics-Element in die Datei Web.config hinzu, wie im folgenden Beispiel gezeigt:

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample57.html?highlight=17-72)]

Wenn Sie die Anwendung in Visual Studio ausführen, sehen Sie in die Protokollen der **Ausgabe** Fenster.

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a>Das Client-Methoden aufrufen und Verwalten von Gruppen von außerhalb der Hub-Klasse

Um den Client von einer anderen Klasse als die Hub-Klasse Methoden aufrufen, rufen Sie einen Verweis auf das SignalR-Context-Objekt, für den Hub, und verwenden Sie, die zum Aufrufen von Methoden auf dem Client oder Verwalten von Gruppen.

Im folgenden Beispiel `StockTicker` Klasse ruft die Kontextobjekt ab, speichert es in eine Instanz der Klasse, speichert die Instanz der Klasse in einer statischen Eigenschaft und verwendet den Kontext aus der Instanz des Singleton-Klasse zum Aufrufen der `updateStockPrice` Methode für Clients verbunden mit einem Hub, mit dem Namen `StockTickerHub`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample58.cs?highlight=8,24)]

Wenn Sie die Kontext-Multiple-Zeiten in einem dauerhaften-Objekt verwenden müssen, rufen Sie den Verweis einmal, und speichern Sie, anstatt ihn jedes Mal abrufen. Abrufen von Kontext einmal wird sichergestellt, dass es sich bei SignalR für Clients in derselben Reihenfolge Senden von Nachrichten in der Ihre hubmethoden Client Methodenaufrufe vornehmen. Ein Lernprogramm, das zeigt, wie Sie mit dem SignalR-Kontext für einen Hub, finden Sie unter [Serverübertragung mit ASP.NET SignalR](index.md).

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a>Aufrufen von Clientmethoden

Sie können angeben, welche Clients die RPC erhält, aber haben weniger Optionen als beim Aufruf von einer hubklasse. Der Grund dafür ist, dass der Kontext kein bestimmter Aufruf von einem Client zugeordnet ist, damit alle Methoden, die Kenntnisse über die aktuellen Verbindungs-ID, z. B. erfordern `Clients.Others`, oder `Clients.Caller`, oder `Clients.OthersInGroup`, sind nicht verfügbar. Die folgenden Optionen sind verfügbar:

- Alle verbundenen Clients.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample59.cs)]
- Einen bestimmten Client identifiziert, die vom Verbindungs-ID.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample60.css)]
- Alle verbundenen Clients mit Ausnahme der angegebenen Clients identifizierte Verbindungs-ID.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample61.cs)]
- Alle verbundenen Clients in einer angegebenen Gruppe.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample62.css)]
- Alle verbundenen Clients in einer angegebenen Gruppe mit Ausnahme der angegebenen Clients identifiziert, die vom Verbindungs-ID.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample63.cs)]

Wenn Sie in Ihre nicht-Hub-Klasse von Methoden in der Hub-Klasse aufrufen, können Sie in der aktuellen Verbindungs­id übergeben und verwenden, die mit `Clients.Client`, `Clients.AllExcept`, oder `Clients.Group` simulieren `Clients.Caller`, `Clients.Others`, oder `Clients.OthersInGroup`. Im folgenden Beispiel die `MoveShapeHub` Klasse übergibt die Verbindungs-ID, die `Broadcaster` Klasse, damit die `Broadcaster` Klasse kann simulieren `Clients.Others`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample64.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a>Verwalten der Gruppenmitgliedschaft

Zum Verwalten von Gruppen müssen Sie die gleichen Optionen wie in einer hubklasse.

- Hinzufügen eines Clients zu einer Gruppe

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample65.cs)]
- Entfernen Sie einen Client aus einer Gruppe

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample66.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a>Gewusst wie: Anpassen die Pipeline Hubs

SignalR ermöglicht Ihnen, Ihren eigenen Code in die Hub-Pipeline einzufügen. Das folgende Beispiel zeigt ein benutzerdefiniertes Hub-Pipeline-Modul, das jeden eingehenden Aufruf einer Methode empfangen aus dem Client und der ausgehende Aufruf der Methode, die aufgerufen wird, auf dem Client protokolliert:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample67.cs)]

Im folgenden code in die *"Global.asax"* Datei registriert das Modul in die Hub-Pipeline ausführen:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample68.cs?highlight=3)]

Es gibt viele verschiedene Methoden, die Sie überschreiben können. Eine vollständige Liste finden Sie unter [HubPipelineModule Methoden](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).
