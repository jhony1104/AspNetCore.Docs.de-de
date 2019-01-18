---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: ASP.NET SignalR-Hubs-API-Leitfaden – JavaScript-Client | Microsoft-Dokumentation
author: pfletcher
description: Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für SignalR-Version 2 in JavaScript-Clients wie Browsern und Windows Store (WinJS) Application...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 12d675b6a2f2f6acdd8c3a5d0d27b5ad2fb1efc4
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396310"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a>ASP.NET SignalR-Hubs-API-Leitfaden – JavaScript-Client
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument enthält eine Einführung zur Verwendung von den Hubs-API für SignalR-Version 2 in JavaScript-Clients wie Browsern und Anwendungen für Windows Store (WinJS).
>
> Der SignalR-Hubs-API können Sie die Remoteprozeduraufrufe (RPCs) von einem Server verbundene Clients und von den Clients an den Server vornehmen. Im Server-Code Sie Methoden definieren, die von Clients aufgerufen werden können, und rufen Sie Methoden, die auf dem Client ausgeführt. Im Clientcode Sie Methoden definieren, die vom Server aufgerufen werden können, und rufen Sie Methoden, die auf dem Server ausgeführt. SignalR ist für alle Client-zu-Server sich für Sie übernimmt.
>
> SignalR bietet außerdem eine Low-Level-API wird aufgerufen, dauerhafte Verbindungen. Eine Einführung in SignalR-Hubs und dauerhafte Verbindungen, finden Sie unter [Einführung zu SignalR](../getting-started/introduction-to-signalr.md).
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

- [Den generierten Proxy und was dies für Sie übernimmt.](#genproxy)

    - [Verwenden Sie den generierten proxy](#cantusegenproxy)
- [Client-Setup](#clientsetup)

    - [Wie Sie auf den dynamisch generierten Proxy verweisen.](#dynamicproxy)
    - [Vorgehensweise: erstellen eine physische Datei für SignalR generierter proxy](#manualproxy)
- [Gewusst wie: Herstellen einer Verbindung](#establishconnection)

    - [$. connection.hub ist das gleiche Objekt erstellt, $.hubConnection()](#connequivalence)
    - [Asynchrone Ausführung der Start-Methode](#asyncstart)
- [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](#crossdomain)
- [Gewusst wie: Konfigurieren der Verbindung](#configureconnection)

    - [Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter](#querystring)
    - [Das Angeben der Transportmethode](#transport)
- [Wie Sie einen Proxy für einen Hub-Klasse abrufen](#getproxy)
- [Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können](#callclient)
- [Gewusst wie: Servermethoden vom Client aufrufen.](#callserver)
- [Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung](#connectionlifetime)
- [Gewusst wie: Behandeln von Fehlern](#handleerrors)
- [Gewusst wie: Aktivieren der clientseitigen Protokollierung](#logging)

Dokumentation zum Erstellen des Servers oder Clients mit .NET zu programmieren finden Sie unter den folgenden Ressourcen:

- [SignalR-Hubs-API-Guide - Server](hubs-api-guide-server.md)
- [Leitfaden für SignalR Hubs-API – .NET-Client](hubs-api-guide-net-client.md)

Die SignalR-2-Serverkomponente ist nur verfügbar unter .NET 4.5, (obwohl es ein .NET Client für SignalR 2 auf .NET 4.0 gibt).

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a>Den generierten Proxy und was dies für Sie übernimmt.

Sie können einen JavaScript-Client für die Kommunikation mit einem SignalR-Dienst, mit oder ohne einen Proxy, den für Sie SignalR generiert programmieren. Funktionsweise der Proxy für Sie ist die Syntax des Codes vereinfachen, die Sie die Verbindung verwenden, Write-Methoden, die der Server aufruft, und rufen Methoden auf dem Server.

Beim Schreiben von Code zum Aufrufen von Servermethoden, der generierte Proxy können Sie Syntax verwenden, die aussieht, als ob Sie eine lokale Funktion ausgeführt haben: Sie können schreiben `serverMethod(arg1, arg2)` anstelle von `invoke('serverMethod', arg1, arg2)`. Die Syntax des generierten Proxys kann auch einen sofortigen und entpackte clientseitige Fehler, wenn Sie einen Server-Methodennamen richtig eingegeben. Und wenn Sie die Datei, die definiert, die Proxys manuell erstellen, erhalten Sie auch IntelliSense-Unterstützung für das Schreiben von Code, der Servermethoden aufruft.

Nehmen wir beispielsweise an, dass Sie die folgende hubklasse auf dem Server verfügen:

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

Die folgenden Codebeispiele zeigen, wie sieht der JavaScript-Code zum Aufrufen der `NewContosoChatMessage` Methode auf dem Server und Empfangen von Aufrufen von der `addContosoChatMessageToPage` Methode auf dem Server.

**Mit den generierten proxy**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

**Ohne den generierten proxy**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a>Verwenden Sie den generierten proxy

Wenn Sie mehrere Ereignishandler für eine Clientmethode zu registrieren, die der Server ruft möchten, können nicht Sie den generierten Proxy verwenden. Andernfalls, Sie können auch den generierten Proxy verwenden oder nicht basierend auf Ihrer Codierung Einstellung. Wenn Sie nicht ihn verwenden, Sie müssen keine verweisen die URL "Signalr/Hubs" in eine `script` Element im Clientcode.

<a id="clientsetup"></a>

## <a name="client-setup"></a>Client-setup

Verweise auf jQuery und die SignalR Core JavaScript-Datei wird von ein JavaScript-Client benötigt. Die jQuery-Version muss es sich um 1.6.4 oder höher Hauptversionen, z. B. 1.7.2, 1.8.2 oder 1.9.1 sein. Wenn Sie den generierten Proxy verwenden möchten, benötigen Sie auch einen Verweis auf den Proxy generiert, SignalR JavaScript-Datei. Das folgende Beispiel zeigt, wie die Verweise in einer HTML-Seite aussehen könnte, die den generierten Proxy verwendet wird.

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

Diese Verweise in folgender Reihenfolge enthalten sein müssen: jQuery Vorname, Nachname von SignalR Core danach und SignalR-Proxys.

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a>Wie Sie auf den dynamisch generierten Proxy verweisen.

Im vorherigen Beispiel wird der Verweis auf den generierten SignalR-Proxy für dynamisch generierte JavaScript-Code, nicht zu einer physischen Datei. SignalR JavaScript-Code für den Proxy dynamisch erstellt und zeigt es an den Client als Antwort auf die URL "/ Signalr/Hubs". Wenn Sie zuvor angegeben haben, eine andere base-URL für SignalR-Verbindungen auf dem Server in Ihrer `MapSignalR` -Methode, die URL für die dynamisch generierte Proxydatei lautet die benutzerdefinierte URL mit "/ Hubs" angefügt.

> [!NOTE]
> Verwenden Sie für Windows 8 (Windows Store) JavaScript-Clients die physischen Proxydatei statt der dynamisch generierten aus. Weitere Informationen finden Sie unter [Vorgehensweise: erstellen eine physische Datei für SignalR generierter Proxy](#manualproxy) weiter unten in diesem Thema.


Verwenden Sie in ASP.NET MVC 4 oder 5 Razor-Ansicht die Tilde zum Verweisen auf das Stammverzeichnis der Anwendung in Ihrem Proxy Dateiverweis aus:

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

Weitere Informationen zur Verwendung von SignalR in MVC 5 finden Sie unter [erste Schritte mit SignalR und MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

Verwenden Sie in einer ASP.NET MVC 3 Razor-Ansicht `Url.Content` Proxy-Datei als Referenz:

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

Verwenden Sie in einer ASP.NET Web Forms-Anwendung `ResolveClientUrl` von Proxys für Datei Verweis, oder registrieren Sie ihn über das ScriptManager, die mit einem relativen Pfad Stamm app (beginnend mit einer Tilde):

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

Verwenden Sie als allgemeine Regel die gleiche Methode zum Angeben der URL "/ Signalr/Hubs", die Sie für CSS oder JavaScript-Dateien verwenden. Wenn Sie eine URL angeben, ohne mit einer Tilde, wird in einigen Szenarien Ihrer Anwendung fehlerfrei, wenn Sie in Visual Studio mit IIS Express testen jedoch mit einen 404-Fehler fehl schlägt, wenn Sie in der vollständigen Variante von IIS bereitstellen. Weitere Informationen finden Sie unter **Auflösen von Verweisen auf Ressourcen auf Stammebene** in [Webserver in Visual Studio für ASP.NET-Webprojekte](https://msdn.microsoft.com/library/58wxa9w5.aspx) auf der MSDN-Website.

Wenn Sie ein Webprojekt in Visual Studio 2017 im Debugmodus ausgeführt, und wenn Sie Internet Explorer als Standardbrowser verwenden, Sie die Proxydatei im sehen **Projektmappen-Explorer** unter **Skripts**.

Um den Inhalt der Datei anzuzeigen, doppelklicken Sie auf **Hubs**. Wenn Sie nicht Visual Studio 2012 oder 2013 und Internet Explorer verwenden oder wenn Sie nicht im Debugmodus sind, können Sie auch den Inhalt der Datei abrufen, indem Sie zu der URL "/ SignalR/Hubs". Wenn an Ihrem Standort ausgeführt wird z. B. `http://localhost:56699`, wechseln Sie zu `http://localhost:56699/SignalR/hubs` in Ihrem Browser.

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a>Vorgehensweise: erstellen eine physische Datei für SignalR generierter proxy

Als Alternative zu den dynamisch generierten Proxy können Sie eine physikalische Datei mit den Proxycode zu erstellen und auf diese Datei verweisen. Sie sollten dies für die Kontrolle über die Zwischenspeicherung oder die Bündelung Verhalten oder IntelliSense zu erhalten, wenn Sie Aufrufe von Servermethoden codieren.

Um eine Proxydatei zu erstellen, führen Sie die folgenden Schritte aus:

1. Installieren Sie die [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet-Paket.
2. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu der *Tools* Ordner, der die SignalR.exe-Datei enthält. Der Ordner "Tools" ist an folgendem Speicherort:

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. Geben Sie folgenden Befehl ein:

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    Der Pfad zu Ihrer *DLL* ist in der Regel die *Bin* Ordner in Ihrem Projektordner.

    Dieser Befehl erstellt eine Datei namens *"Server.js"* im gleichen Ordner wie *signalr.exe*.
4. Platzieren der *"Server.js"* Datei in einem geeigneten Ordner in Ihrem Projekt, benennen Sie sie nach Bedarf für Ihre Anwendung und einen Verweis darauf anstelle der "Signalr/Hubs" Verweis hinzufügen.

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Gewusst wie: Herstellen einer Verbindung

Bevor Sie eine Verbindung herstellen können, müssen Sie ein Verbindungsobjekt zu erstellen, erstellen Sie einen Proxy und Registrierung von Ereignishandlern für Methoden, die vom Server aufgerufen werden können. Wenn der Proxy und die Ereignishandler eingerichtet sind, Herstellung einer Verbindung durch Aufrufen der `start` Methode.

Wenn Sie den generierten Proxy verwenden, müssen Sie das Verbindungsobjekt in Ihrem eigenen Code zu erstellen, da der generierte Proxycode für Sie übernimmt.

<a id="nogenconnection"></a>

**Herstellen einer Verbindung (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

**Herstellen einer Verbindung (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service. WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).

Standardmäßig ist der Hub-Speicherort dem aktuellen Server; Wenn Sie auf einen anderen Server herstellen, geben Sie die URL vor dem Aufruf der `start` Methode, wie im folgenden Beispiel gezeigt:

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> Normalerweise der Registrierung von Ereignishandlern vor dem Aufruf der `start` Methode zum Herstellen der Verbindung. Wenn einige Ereignishandler zu registrieren, nachdem die Verbindung hergestellt werden soll, können Sie dies, aber Sie müssen mindestens eines Ihrer einen oder mehrere Ereignishandler vor dem Aufruf Registrieren der `start` Methode. Ein Grund hierfür ist, dass in einer Anwendung kann viele Hubs vorhanden sein, aber nicht empfehlenswert Auslösen der `OnConnected` Ereignis für jede Hub-Instanz, wenn Sie nur einen der Verträge verwenden möchten. Wenn die Verbindung hergestellt ist, das Vorhandensein einer Clientmethode auf einem Hub-Proxy ist, was SignalR auslösen soll die `OnConnected` Ereignis. Wenn Sie sich nicht die Ereignishandler vor dem Aufruf Registrieren der `start` -Methode, Sie werden zum Aufrufen von Methoden in den Hub, aber der Hub `OnConnected` Methode nicht aufgerufen und keine Clientmethoden werden vom Server aufgerufen werden.


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a>$. connection.hub ist das gleiche Objekt erstellt, $.hubConnection()

Wie Sie aus den Beispielen sehen können, wenn Sie den generierten Proxy verwenden `$.connection.hub` bezieht sich auf das Verbindungsobjekt. Dies ist das gleiche Objekt, das Sie durch den Aufruf erhalten `$.hubConnection()` kein bei den generierten Proxy verwenden. Der generierte Proxycode wird die Verbindung für Sie erstellt, durch die folgende Anweisung ausführen:

![Erstellen eine Verbindung in die generierte Proxydatei](hubs-api-guide-javascript-client/_static/image3.png)

Wenn Sie den generierten Proxy verwenden, möchten Sie etwas `$.connection.hub` , Sie mit einem Verbindungsobjekt durchführen können, wenn Sie den generierten Proxy nicht verwenden.

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a>Asynchrone Ausführung der Start-Methode

Wenn die `start` Methode asynchron ausgeführt wird. Gibt eine [jQuery verzögerten Objekt](http://api.jquery.com/category/deferred-object/), was bedeutet, dass Sie die Rückruffunktionen hinzufügen können, durch Aufrufen von Methoden wie z. B. `pipe`, `done`, und `fail`. Wenn Sie über Code, die verfügen, die nach dem Herstellen der Verbindung ausgeführt werden sollen, z. B. einen Aufruf an eine Servermethode, fügen Sie diesen Code in eine Callback-Funktion, oder aus einer Rückruffunktion aufgerufen Sie wird. Die `.done` Callback-Methode wird immer dann ausgeführt, nachdem die Verbindung hergestellt wurde, und nachdem alle code Sie in haben Ihrem `OnConnected` Ereignishandlermethode auf dem Server die Ausführung abgeschlossen ist.

Wenn Sie die Anweisung "Jetzt verbunden" aus dem vorherigen Beispiel als die nächste Codezeile nach dem Einfügen der `start` Methodenaufruf (nicht in eine `.done` Rückruf), wird die `console.log` Zeile ausgeführt, bevor die Verbindung hergestellt wird, wie im folgenden dargestellt Beispiel:

![Falsche Methode zum Schreiben von Code, der ausgeführt wird, nachdem die Verbindung hergestellt wird](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a>Gewusst wie: Herstellen einer Verbindung domänenübergreifende

In der Regel, wenn der Browser eine Seite lädt `http://contoso.com`, die SignalR-Verbindung ist auf der gleichen Domäne `http://contoso.com/signalr`. Wenn die Seite `http://contoso.com` stellt eine Verbindung mit `http://fabrikam.com/signalr`, d. h. eine domänenübergreifende-Verbindung. Aus Sicherheitsgründen sind die domänenübergreifende Verbindungen standardmäßig deaktiviert.

In SignalR 1.x, domänenübergreifende Anforderungen wurden gesteuert, indem einem einzigen EnableCrossDomain-Flag. Dieses Flag gesteuert, sowohl JSONP und CORS-Anforderungen. Zur Erhöhung der Flexibilität-alle CORS-Unterstützung wurde aus dem die Server-Komponente von SignalR entfernt (JavaScript-Clients weiterhin verwenden CORS normalerweise, wenn es erkannt wird, dass der Browser unterstützt), und neue OWIN-Middleware zur Unterstützung dieser Szenarios verfügbar gemacht wurde.

Wenn JSONP auf dem Client (domänenübergreifende Anforderungen in älteren Browsern unterstützen) erforderlich ist, müssen sie explizit aktiviert werden, durch Festlegen von `EnableJSONP` auf die `HubConfiguration` -Objekt `true`, wie unten dargestellt. JSONP ist standardmäßig deaktiviert, da er als weniger sicher als CORS ist.

**Microsoft.Owin.Cors und dem Projekt hinzugefügt:** Um diese Bibliothek zu installieren, führen Sie den folgenden Befehl in der Paket-Manager-Konsole ein:

`Install-Package Microsoft.Owin.Cors`

Dieser Befehl fügt die 2.1.0 Version des Pakets zu Ihrem Projekt.

### <a name="calling-usecors"></a>Aufrufen von "usecors"

 Der folgende Codeausschnitt veranschaulicht, wie domänenübergreifende Verbindungen in SignalR 2. implementiert wird.

**Implementieren von domänenübergreifende Anforderungen in SignalR 2**

Der folgende Code veranschaulicht, wie CORS oder JSONP in einem SignalR-2-Projekt zu aktivieren. Dieses Codebeispiel verwendet `Map` und `RunSignalR` anstelle von `MapSignalR`, sodass nur für die SignalR-Anforderungen, die CORS-Unterstützung erfordern, die CORS-Middleware ausgeführt wird (und nicht für den gesamten Datenverkehr an den im angegebenen Pfad `MapSignalR`.) Map kann auch für andere Middleware, die verwendet werden, die für eine bestimmte URL-Präfix, nicht aber für die gesamte Anwendung ausgeführt werden muss.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - Legen Sie nicht `jQuery.support.cors` auf "true" in Ihrem Code.
>
>     ![Legen Sie jQuery.support.cors nicht auf "true"](hubs-api-guide-javascript-client/_static/image7.png)
>
>     SignalR behandelt die Verwendung von CORS. Festlegen von `jQuery.support.cors` auf "true" JSONP deaktiviert, da SignalR davon aus, der Browser das CORS unterstützt wird.
> - Wenn Sie eine Verbindung zu einem Localhost-URL herstellen, wird nicht Internet Explorer 10 eine Verbindung domänenübergreifende beachtet werden, damit die Anwendung lokal mit Internet Explorer 10 funktionieren, auch wenn Sie die domänenübergreifende Verbindungen auf dem Server aktiviert haben.
> - Weitere Informationen zur Verwendung von domänenübergreifende Verbindungen mit Internet Explorer 9, finden Sie unter [dieser Stack Overflow-Thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).
> - Weitere Informationen zur Verwendung von domänenübergreifende Verbindungen mit Chrome finden Sie unter [dieser Stack Overflow-Thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).
> - Der Beispielcode verwendet die Standardeinstellung "/ Signalr" die URL für die Verbindung mit Ihrem SignalR Service. WPF-Clientcode für die Methode wird vom Server ohne Parameter aufgerufen. [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Gewusst wie: Konfigurieren der Verbindung

Bevor Sie eine Verbindung herstellen, können Sie Abfragezeichenfolgen-Parameter angeben oder geben Sie die Transportmethode.

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Gewusst wie: Angeben von Abfragezeichenfolgen-Parameter

Wenn Sie möchten die Daten an den Server gesendet, wenn der Client eine Verbindung herstellt, können Sie Abfragezeichenfolgen-Parameter an das Verbindungsobjekt hinzufügen. Die folgenden Beispiele zeigen, wie Sie einen Abfragezeichenfolgen-Parameter in Client-Code festlegen.

**Legen Sie einen Zeichenfolgenwert für die Abfrage vor dem Aufrufen der Start-Methode (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

**Legen Sie einen Zeichenfolgenwert für die Abfrage vor dem Aufrufen der Start-Methode (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

Das folgende Beispiel zeigt, wie Sie einen Abfragezeichenfolgen-Parameter im Servercode zu lesen.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Das Angeben der Transportmethode

Als Teil der Prozess der verbindungsherstellung verhandelt ein SignalR-Client normalerweise mit dem Server um den besten Transport zu bestimmen, der unterstützt wird, indem sowohl Server-als auch. Wenn Sie bereits wissen, dass Transport Sie verwenden möchten, können Sie dieser Aushandlungsprozess umgehen, indem Sie die Transportmethode angeben, beim Aufrufen der `start` Methode.

**Clientcode, der angibt, die Transportmethode (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

**Clientcode, der angibt, die Transportmethode (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

Als Alternative können Sie mehrere Transportmethoden in der Reihenfolge angeben, in denen Sie SignalR, wenn sie Sie testen möchten:

**Clientcode, der angibt, ein benutzerdefinierter Transport-fallback-Schema (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

**Clientcode, der angibt, ein benutzerdefinierter Transport-fallback-Schema (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

Sie können die folgenden Werte verwenden, für die Angabe der Transportmethode die:

- "webSockets"
- "foreverFrame"
- "serverSentEvents"
- "LongPolling"

Die folgenden Beispiele zeigen, wie Sie herausfinden, welcher Transportmethode von einer Verbindung verwendet wird.

**Clientcode, der die Transportmethode ein, die eine Verbindung (mit den generierten Proxy) wird angezeigt.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

**Clientcode, der die Transportmethode, die von einer Verbindung (ohne den generierten Proxy) verwendete zeigt**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

Informationen zum Überprüfen der Transportmethode in Server-Code finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server – zum Abrufen von Informationen über den Client aus der Kontexteigenschaft](hubs-api-guide-server.md#contextproperty). Weitere Informationen zu Transporten und Fallbacks, finden Sie unter [Einführung zu SignalR - Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports).

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a>Wie Sie einen Proxy für einen Hub-Klasse abrufen

Jede Verbindungsobjekt, das Sie erstellen kapselt Informationen über eine Verbindung mit einem SignalR-Dienst, der eine oder mehrere Klassen von Hub enthält. Mit einer hubklasse kommunizieren kann, verwenden Sie ein Proxyobjekt, das Sie erstellen selbst (sofern Sie nicht über den generierten Proxy verwenden) oder die für Sie generiert wird.

Auf dem Client ist der Proxyname einer Version in Kamel-Schreibweise des Klassennamens Hub. SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann.

**Hub-Klasse auf server**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

**Abrufen eines Verweises auf den generierten Client-Proxy für den Hub**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

**Erstellen von Clientproxy für die hubklasse (ohne generierten Proxy)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

Wenn Sie ergänzen, um Ihre hubklasse mit einem `HubName` -Attributs festzulegen, verwenden Sie den genauen Namen ohne die Groß-/Kleinschreibung ändern.

**Hub-Klasse, auf dem Server mit HubName-Attribut**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

**Abrufen eines Verweises auf den generierten Client-Proxy für den Hub**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

**Erstellen von Clientproxy für die hubklasse (ohne generierten Proxy)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Wie Sie Methoden auf dem Client zu definieren, die der Server aufgerufen werden können

Um eine Methode definieren, die der Server über einen Hub aufrufen können, fügen Sie einen Ereignishandler auf die Hubproxy-Klasse unter Verwendung der `client` Eigenschaft des generierten Proxys oder der Aufruf der `on` Methode, wenn Sie den generierten Proxy nicht verwenden. Die Parameter können komplexe Objekte sein.

Fügen Sie den Ereignishandler vor dem Aufruf der `start` Methode zum Herstellen der Verbindung. (Wenn Sie nach dem Aufruf Ereignishandler hinzufügen möchten die `start` -Methode finden Sie unter den Hinweis im [Gewusst wie: Herstellen einer Verbindung](#establishconnection) weiter oben in diesem Dokument, und verwenden Sie die Syntax für die Definition einer Methode ohne Verwendung des generierten Proxys angezeigt.)

Methode-namenszuordnung wird Groß-/Kleinschreibung. Z. B. `Clients.All.addContosoChatMessageToPage` auf dem Server führt `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, oder `addcontosochatmessagetopage` auf dem Client.

**Definieren Sie die Methode auf dem Client (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

**Alternative Möglichkeit zum Definieren einer Methode auf dem Client (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

**Definieren Sie die Methode auf dem Client (ohne den generierten Proxy, oder wenn Sie nach dem Aufrufen der Start-Methode hinzufügen)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

**Server-Code, der die Methode aufruft.**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

Die folgenden Beispiele enthalten ein komplexes Objekt als Methodenparameter angegeben.

**Definieren Sie die Methode auf Client, der ein komplexes Objekt (mit den generierten Proxy) akzeptiert.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

**Definieren Sie die Methode auf Client, der ein komplexes Objekt (ohne den generierten Proxy) akzeptiert.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

**Server-Code, die das komplexe Objekt definiert.**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

**Servercode, der die Clientmethode, die mit der ein komplexes Objekt aufruft**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Gewusst wie: Servermethoden vom Client aufrufen.

Um eine Servermethode auf dem Client aufzurufen, verwenden die `server` Eigenschaft des generierten Proxys oder `invoke` Methode für die Hubproxy-Klasse, wenn Sie den generierten Proxy nicht verwenden. Der Rückgabewert oder Parameter können komplexe Objekte sein.

Eine Camel-Case-Version des Methodennamens für den Hub übergeben. SignalR erstellt automatisch diese Änderung, damit JavaScript-Code JavaScript-Konventionen entsprechen kann.

Die folgenden Beispiele zeigen, wie Sie eine Servermethode aufrufen, die nicht über einen Rückgabewert verfügt und wie Sie eine Servermethode aufrufen, die einen Rückgabewert verfügt.

**Server-Methode ohne HubMethodName-Attribut**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

**Servercode, der definiert, das komplexe Objekt an den Parameter übergeben**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

Wenn Sie die Hub-Methode mit ergänzt einen `HubMethodName` -Attributs festzulegen, verwenden Sie diesen Namen ohne die Groß-/Kleinschreibung ändern.

**Server-Methode** mit einem HubMethodName-Attribut

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

Im vorherigen Beispiel veranschaulicht eine Servermethode aufrufen, die keinen zurückgibt Wert. Die folgenden Beispiele zeigen, wie Sie eine Servermethode aufrufen, die über einen Rückgabewert verfügt.

**Server-Code für eine Methode, die über einen Rückgabewert verfügt.**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

**Die Stock-Klasse für den** Rückgabewert

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

**Clientcode, der die Server-Methode (mit den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

**Clientcode, der die Server-Methode (ohne den generierten Proxy) aufruft.**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Gewusst wie: Behandeln der Objektlebensdauer-Ereignisse der Verbindung

SignalR bietet die folgenden Verbindung, die Objektlebensdauer-Ereignisse, die Sie behandeln können:

- `starting`: Wird ausgelöst, bevor alle Daten über die Verbindung gesendet werden.
- `received`: Wird ausgelöst, wenn alle Daten für die Verbindung empfangen werden. Stellt die empfangenen Daten bereit.
- `connectionSlow`: Wird ausgelöst, wenn der Client eine Verbindung langsame ist oder häufig löschen erkennt.
- `reconnecting`: Wird ausgelöst, wenn des zugrunde liegenden Transports, erneut eine Verbindung herzustellen beginnt.
- `reconnected`: Wird ausgelöst, wenn der zugrunde liegenden Transport die Verbindung wiederhergestellt wurde.
- `stateChanged`: Wird ausgelöst, wenn der Verbindungsstatus ändert. Enthält den alten Status und den neuen Zustand (Herstellen einer Verbindung, verbunden, Verbindung oder Disconnected).
- `disconnected`: Wird ausgelöst, wenn die Verbindung getrennt wurde.

Beispielsweise sollten Sie Warnmeldungen angezeigt, wenn Probleme mit der Verbindung, die nennenswerte Verzögerungen verursachen können, behandelt der `connectionSlow` Ereignis.

**Behandeln Sie das ConnectionSlow-Ereignis (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

**Behandeln des Ereignisses ConnectionSlow (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

Weitere Informationen finden Sie unter [verstehen und Behandeln von Ereignissen für Lebensdauer in SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Gewusst wie: Behandeln von Fehlern

Der SignalR-JavaScript-Client enthält eine `error` -Ereignis, das Sie einen Handler für hinzufügen können. Sie können auch die Fail-Methode verwenden, um einen Handler für Fehler hinzuzufügen, die aus einem Methodenaufruf für den Server.

Wenn Sie detaillierte Fehlermeldungen auf dem Server nicht explizit aktivieren, enthält das Ausnahmeobjekt an, dem SignalR nach einem Fehler zurückgibt minimale Informationen über den Fehler an. Beispielsweise wenn ein Aufruf von `newContosoChatMessage` ein Fehler auftritt, der die Fehlermeldung in das Fehlerobjekt enthält "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Senden detaillierte Fehlermeldungen für Clients in einer produktionsumgebung wird nicht empfohlen aus Sicherheitsgründen aber wenn Sie möchten detaillierte Fehlermeldungen für aktivieren Problembehandlung, verwenden Sie den folgenden Code auf dem Server.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

Das folgende Beispiel zeigt, wie Sie einen Ereignishandler für das Fehlerereignis hinzuzufügen.

**Fügen Sie einen Fehlerhandler (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

**Hinzufügen einer Fehlerbehandlungsroutine (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

Das folgende Beispiel zeigt, wie Sie einen Fehler aus einem Methodenaufruf zu behandeln.

**Behandeln Sie Fehler über den Aufruf einer Methode (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

**Behandeln Sie einen Fehler eines Methodenaufrufs (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

Wenn der Aufruf einer Methode ein Fehler auftritt, die `error` Ereignis wird auch ausgelöst, sodass Ihren Code in die `error` Methodenhandler und klicken Sie in der `.fail` Methode-Rückruf ausgeführt wird.

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Gewusst wie: Aktivieren der clientseitigen Protokollierung

Legen Sie zum Aktivieren der clientseitigen Protokollierung für eine Verbindung der `logging` Eigenschaft für das Verbindungsobjekt vor dem Aufruf der `start` Methode zum Herstellen der Verbindung.

**Aktivieren der Protokollierung (mit den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

**Aktivieren der Protokollierung (ohne den generierten Proxy)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

Um die Protokolle anzuzeigen, öffnen Sie Entwicklertools Ihres Browsers, und wechseln Sie zur Registerkarte "Konsole" aus. Ein Tutorial mit schrittweise Anweisungen und Bildschirm Bildschirmfotos, die zeigen, wie Sie möchten, können, finden Sie [Serverübertragung mit ASP.NET Signalr - Aktivieren der Protokollierung](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).
