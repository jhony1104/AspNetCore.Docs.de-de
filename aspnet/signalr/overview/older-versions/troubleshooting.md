---
uid: signalr/overview/older-versions/troubleshooting
title: Problembehandlung für SignalR (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Dieser Artikel beschreibt allgemeine Probleme bei der Entwicklung von SignalR-Anwendungen.
ms.author: bradyg
ms.date: 06/05/2013
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: d485829fb60b32b063405f37ae9c6c278bf7255b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836271"
---
<a name="signalr-troubleshooting-signalr-1x"></a>Problembehandlung für SignalR (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument beschreibt allgemeine Probleme mit SignalR.


Dieses Dokument enthält die folgenden Abschnitte.

- [Aufrufen von Methoden, die zwischen Client und Server im Hintergrund ein Fehler auftritt](#connection)
- [Anderen bestehenden Verbindungsprobleme](#other)
- [Kompilierung und serverseitigen Fehlern](#server)
- [Probleme bei Visual Studio](#vs)
- [IIS-Probleme](#iis)
- [Probleme mit Azure](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a>Aufrufen von Methoden, die zwischen Client und Server im Hintergrund ein Fehler auftritt

Dieser Abschnitt beschreibt mögliche Ursachen für einen Methodenaufruf zwischen Client und Server ohne eine sinnvolle Fehlermeldung fehl. In einer SignalR-Anwendung verfügt der Server keine Informationen über die Methoden, die der Client implementiert; Wenn der Server eine Clientmethode aufruft, die Methode und die Daten an den Client gesendet werden, und die Methode wird ausgeführt, nur dann, wenn sie in das Format vorhanden ist, die der Server angegeben. Wenn auf dem Client keine übereinstimmende Methode gefunden wird, geschieht nichts, und auf dem Server wird keine Fehlermeldung ausgegeben.

Zur weiteren Untersuchung Methode nicht aufgerufen, können Sie Protokollierung vor dem Aufrufen die Start-Methode auf den Hub, um welche Aufrufe angezeigt werden vom Server eingehenden aktivieren. Zum Aktivieren der Protokollierung in einer JavaScript-Anwendung, finden Sie unter [Aktivieren der clientseitigen Protokollierung (JavaScript-Client-Version)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging). Zum Aktivieren der Protokollierung in einer .NET Client-Anwendung, finden Sie unter [Aktivieren der clientseitigen Protokollierung (.NET Client-Version)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a>Falsch geschriebene Methode, falschen Methodensignatur oder falsche Hub-name

Wenn der Name oder die Signatur einer aufgerufenen Methode eine geeignete Methode auf dem Client nicht genau übereinstimmt, schlägt der Aufruf fehl. Stellen Sie sicher, dass der Name der Methode wird aufgerufen, durch den Server mit dem Namen der Methode auf dem Client übereinstimmt. SignalR erstellt außerdem die hubproxy-Klasse, die mit der Camel-Case-Methoden, wie in JavaScript geeignet ist, also eine Methode namens `SendMessage` auf dem Server aufgerufen `sendMessage` in den Clientproxy. Bei Verwendung der `HubName` Attribut im serverseitigen Code, stellen Sie sicher, dass der verwendete Name den Namen, die zum Erstellen des Hubs auf dem Client entspricht. Wenn Sie nicht verwenden, die `HubName` Attribut, stellen Sie sicher, dass der Name des Hubs in einem JavaScript-Client in Kamel-Schreibweise, wie z. B. ChatHub statt ChatHub.

### <a name="duplicate-method-name-on-client"></a>Doppelte Methodennamen auf client

Stellen Sie sicher, dass Sie auf dem Client, der sich nur durch Fall unterscheidet, nicht über eine doppelte Methode verfügen. Wenn die Clientanwendung eine Methode namens verfügt `sendMessage`, stellen Sie sicher, dass noch kein auch eine Methode namens `SendMessage` ebenfalls.

### <a name="missing-json-parser-on-the-client"></a>Fehlender JSON-Parser auf dem client

SignalR ist erforderlich, einen JSON-Parser zum Serialisieren von Aufrufen zwischen dem Server und dem Client vorhanden sein. Wenn der Client nicht über einen integrierten JSON-Parser (z. B. Internet Explorer 7) verfügt, müssen Sie in Ihrer Anwendung enthalten. Sie können die JSON-Parsers [hier](http://nuget.org/packages/json2).

### <a name="mixing-hub-and-persistentconnection-syntax"></a>Mischen von Hub und PersistentConnection-syntax

SignalR verwendet zwei Kommunikationsmodelle: Hubs und PersistentConnections. Die Syntax zum Aufrufen dieser beiden Kommunikationsmodelle unterscheidet sich in den Clientcode. Wenn Sie einen Hub in Ihrem Code hinzugefügt haben, stellen Sie sicher, dass alle von Ihrem Client-Code verwendet die entsprechenden Hub-Syntax.

**JavaScript-Clientcode, der eine PersistentConnection in einem JavaScript-Client erstellt.**

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

**JavaScript-Clientcode, mit dem Proxy für einen Hub in einem Javascript-Client erstellt.**

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

**C#-Server-Code, der eine Route einer PersistentConnection zugeordnet**

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

**C#-Server-Code, der eine Route mit einem Hub oder an mehrere Hubs zugeordnet, wenn mehrere Anwendungen**

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a>Verbindung gestartet, bevor Abonnements hinzugefügt werden

Wenn die Verbindung des Hubs gestartet wurde, bevor Methoden, die vom Server aufgerufen werden können, mit dem Proxy hinzugefügt werden, werden keine Nachrichten empfangen. Die folgende JavaScript-Code wird den Hub nicht ordnungsgemäß gestartet:

**Falsche JavaScript-Clientcode, der nicht mit Hubs Nachrichten empfangen werden kann**

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

Fügen Sie stattdessen die Methode Abonnements vor dem Aufruf von Start hinzu:

**JavaScript-Clientcode, der von Abonnements mit einem Hub korrekt hinzugefügt**

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a>Fehlende Methodennamen für die hubproxy-Klasse

Stellen Sie sicher, dass die auf dem Server definierte Methode auf dem Client abonniert hat. Auch wenn der Server die Methode definiert, muss es weiterhin an den Clientproxy hinzugefügt werden. Methoden können auf folgende Weise an den Clientproxy hinzugefügt werden (Beachten Sie, das die Methode hinzugefügt wird, die `client` Mitglied im Hub nicht für den Hub direkt):

**JavaScript-Clientcode, mit dem Proxy für einen Hub Methoden hinzugefügt**

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a>Hub "oder" Hub-Methoden, die nicht als öffentlich deklariert.

Um auf dem Client sichtbar sein, müssen als die Hub-Implementierung und Methoden deklariert werden `public`.

### <a name="accessing-hub-from-a-different-application"></a>Zugriff auf Hubs aus einer anderen Anwendung

SignalR-Hubs können nur über Anwendungen zugegriffen werden, die SignalR-Clients zu implementieren. SignalR kann nicht mit anderen kommunikationsbibliotheken (wie SOAP oder WCF-Webdienste.) zusammenarbeiten. Wenn es keine SignalR-Client, die für die Zielplattform verfügbar ist, können nicht Sie des Servers Endpunkt direkt zugreifen.

### <a name="manually-serializing-data"></a>Serialisieren von Daten manuell

SignalR werden automatisch JSON verwenden, um Ihre Methode serialisieren Parameter-dort nicht erforderlich, es selbst tun.

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a>Remote hubmethode nicht ausgeführt wird, auf dem Client in OnDisconnected-Funktion

Dieses Verhalten ist vorgesehen. Wenn `OnDisconnected` ist aufgerufen wird, wurde bereits der Hub eingegeben der `Disconnected` Zustand, der Hub-Methoden aufgerufen werden keine weiteren zulässt.

**C#-Server-Code, der Code ordnungsgemäß im Ereignis OnDisconnected ausführt**

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a>Verbindung für erreicht.

Wenn Sie die Vollversion von IIS auf einem Clientbetriebssystem wie Windows 7 zu verwenden, ist eine 10-Connection-Beschränkung. Wenn Sie ein Clientbetriebssystem verwenden zu können, verwenden Sie IIS Express stattdessen um dieses Limit zu vermeiden.

### <a name="cross-domain-connection-not-set-up-properly"></a>Domänenübergreifende-Verbindung nicht ordnungsgemäß eingerichtet

Wenn eine domänenübergreifende-Verbindung (eine Verbindung für die die SignalR-URL nicht in derselben Domäne wie der Hostseite ist) ist nicht ordnungsgemäß eingerichtet, die Verbindung möglicherweise fehl, ohne eine Fehlermeldung angezeigt. Informationen dazu, wie eine domänenübergreifende Kommunikation zu ermöglichen, finden Sie unter [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a>Verbindung mithilfe von NTLM (Active Directory) funktioniert nicht in .NET client

Eine Verbindung in einer .NET Client-Anwendung, die Sicherheit der Domäne verwendet fehlschlagen, wenn die Verbindung nicht ordnungsgemäß konfiguriert ist. Verwendung von SignalR in einer domänenumgebung, legen Sie die erforderlichen Connection-Eigenschaft wie folgt:

**C#-Clientcode, der Anmeldeinformationen für die Verbindung implementiert.**

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a>Anderen bestehenden Verbindungsprobleme

In diesem Abschnitt wird beschrieben, die Ursachen und Lösungen für spezifische Symptome oder Fehlermeldungen, die während einer Verbindung auftreten.

### <a name="start-must-be-called-before-data-can-be-sent-error"></a>Fehler "Start muss aufgerufen werden, bevor Daten gesendet werden können"

Dieser Fehler wird häufig angezeigt, wenn Code eine SignalR-Objekte verweist, bevor die Verbindung gestartet wird. Das Verbinden für Handler und ähnliches, Methoden aufzurufen, die auf dem Server definierte hinzugefügt werden muss, nachdem die Verbindung hergestellt wird. Beachten Sie, dass der Aufruf von `Start` ist asynchron, sodass Code nach dem der Aufruf, bevor sie ausgeführt werden abgeschlossen wird. Die beste Möglichkeit, den Handler hinzuzufügen, nachdem eine Verbindung vollständig gestartet wurde, ist sie eine Rückruffunktion abgelegt, die als Parameter an die Start-Methode übergeben wird:

**JavaScript-Clientcode ordnungsgemäß-Ereignishandler hinzu, die SignalR-Objekte zu verweisen**

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

Dieser Fehler wird ebenfalls angezeigt werden, wenn eine Verbindung beendet wird, während der SignalR-Objekte sind immer noch verwiesen wird.

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a>"301 Permanent verschoben" oder "302 vorübergehend verschoben"-Fehler

Dieser Fehler kann angezeigt werden, wenn das Projekt enthält einen Ordner namens SignalR, die mit dem automatisch erstellten Proxy beeinträchtigt. Um diesen Fehler zu vermeiden, verwenden Sie keinen Ordner namens `SignalR` in Ihrer Anwendung, oder aktivieren Sie automatische Proxygenerierung deaktiviert. Finden Sie unter [der generierten Proxy und was dies für Sie übernimmt](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) Weitere Details.

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a>"403 – Verboten"-Fehler in .NET oder Silverlight-client

Dieser Fehler kann in Umgebungen mit Domänen hinweg auftreten, in denen domänenübergreifende Kommunikation nicht ordnungsgemäß aktiviert ist. Informationen dazu, wie eine domänenübergreifende Kommunikation zu ermöglichen, finden Sie unter [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain). Um eine domänenübergreifende-Verbindung in einem Silverlight-Client herzustellen, finden Sie unter [domänenübergreifende Verbindungen von Silverlight-Clients](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).

### <a name="404-not-found-error"></a>Fehler "404 nicht gefunden"

Es gibt mehrere Ursachen für dieses Problem. Überprüfen Sie Folgendes aus:

- **Hub-Proxy-Adresse Verweis nicht ordnungsgemäß formatiert:** Dieser Fehler wird häufig angezeigt, wenn der Verweis auf die generierten Hub-Proxy-Adresse nicht korrekt formatiert ist. Stellen Sie sicher, dass der Verweis auf die Hub-Adresse richtig gemacht wird. Finden Sie unter [wie auf den dynamisch generierten Proxy verwiesen](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) Details.
- **Hinzufügen von Routen für die Anwendung vor dem Hinzufügen der hubroute:** Wenn Ihre Anwendung anderen Routen verwendet, stellen Sie sicher, dass die erste Route hinzugefügt, den Aufruf wird `MapHubs`.

### <a name="500-internal-server-error"></a>"500 Interner Serverfehler"

Dies ist ein sehr allgemeiner Fehler, der eine Vielzahl von Ursachen haben. Die Details des Fehlers in das Ereignisprotokoll des Servers angezeigt werden soll, oder finden Sie über das Debuggen des Servers. Ausführlichere Fehlerinformationen kann durch Aktivieren ausführlicher Fehler auf dem Server abgerufen werden. Weitere Informationen finden Sie unter [Gewusst wie: Behandeln von Fehlern in der hubklasse](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).

### <a name="typeerror-lthubtypegt-is-undefined-error"></a>"TypeError: &lt;" HubType "festgelegt&gt; ist nicht definiert" Fehler

Dieser Fehler ausgelöst, wenn der Aufruf von `MapHubs` erfolgt nicht ordnungsgemäß. Finden Sie unter [die SignalR-Route zu registrieren, und konfigurieren Optionen für SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) für Weitere Informationen.

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a>JsonSerializationException wurde nicht vom Benutzercode behandelt.

Stellen Sie sicher, dass die Parameter, die Sie an Ihre Methoden senden keine nicht-serialisierbare Typen (z.B. Dateihandles oder Datenbankverbindungen) enthalten. Wenn Sie Mitglieder für ein serverseitiges Objekt verwenden, die Sie nicht möchten, die an den Client (entweder für die Sicherheit oder aus Gründen der Serialisierung), verwenden gesendet werden müssen die `JSONIgnore` Attribut.

### <a name="protocol-error-unknown-transport-error"></a>"Protokollfehler: Unbekannter Transportfehler "

Dieser Fehler kann auftreten, wenn der Client die Transporte, die SignalR verwendet, nicht unterstützt wird. Finden Sie unter [Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports) Informationen, die auf dem Browser mit SignalR verwendet werden können.

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a>"JavaScript-Hub-Clientproxy-Generierungsprozess wurde deaktiviert."

Dieser Fehler tritt auf, wenn `DisableJavaScriptProxies` festgelegt ist, während Sie auch das Einfügen eines Verweises auf den dynamisch generierten Proxy an `signalr/hubs`. Weitere Informationen zum manuellen Erstellen des Proxys finden Sie unter [des generierten Proxys und was dies für Sie übernimmt](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a>"Die Verbindungs-ID ist im falschen Format" oder "die Identität des Benutzers nicht ändern, während eine aktive SignalR-Verbindung" Fehler

Dieser Fehler kann auftreten, wenn Authentifizierung wird verwendet, und der Client wird abgemeldet, bevor die Verbindung beendet wird. Die Lösung besteht darin, die SignalR-Verbindung, bevor der Client durch die Abmeldung zu beenden.

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a>"Unerwarteter Fehler: SignalR: jQuery, wurde nicht gefunden. Stellen Sie sicher, dass jQuery, bevor die SignalR.js-Datei verwiesen wird"Fehler

Der SignalR-JavaScript-Client erfordert jQuery ausführen. Stellen Sie sicher, dass der Verweis auf die jQuery richtig ist, dass der verwendete Pfad gültig ist und dass der Verweis auf jQuery vor dem Verweis auf SignalR ist.

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a>"Nicht abgefangene TypeError: Eigenschaft kann nicht gelesen werden kann '&lt;Eigenschaft&gt;"undefined" Fehler

Dieser Fehler tritt aus ohne jQuery oder Hubs Proxy ordnungsgemäß verwiesen. Stellen Sie sicher, dass der Verweis auf die jQuery und die Hubs-Proxy richtig ist, dass der verwendete Pfad gültig ist und dass der Verweis auf jQuery vor dem Verweis auf die Hubs-Proxy ist. Standardverweis auf den Proxy Hubs sollte wie folgt aussehen:

**Die clientseitige HTML-Code, der den Hubs Proxy ordnungsgemäß verweist**

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a>Fehler "RuntimeBinderException aus wurde nicht vom Benutzercode behandelt"

Dieser Fehler kann auftreten, wenn die falsche Überladung des `Hub.On` verwendet wird. Wenn die Methode einen Rückgabewert verfügt, muss der Rückgabetyp als einen generischen Typparameter angegeben werden:

**Methode, die definiert, auf dem Client (ohne generierten Proxy)**

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a>Verbindungs­id ist nicht konsistent oder die Verbindung unterbrochen wird, zwischen seitenladevorgänge

Dieses Verhalten ist vorgesehen. Da das Hub-Objekt in das Page-Objekt gehostet wird, wird der Hub zerstört, wenn die Seite aktualisiert. Eine Anwendung auf mehreren Seite muss die Zuordnung zwischen Benutzern und Verbindungs-IDs beibehalten, damit sie zwischen seitenladevorgänge konsistent ist. Der Verbindungs-IDs kann gespeichert werden, auf dem Server entweder einen `ConcurrentDictionary` Objekts oder einer Datenbank.

### <a name="value-cannot-be-null-error"></a>Fehler "Wert darf nicht null sein"

Serverseitige Methoden mit optionalen Parametern werden derzeit nicht unterstützt. Wenn der optionale Parameter ausgelassen wird, wird die Methode fehlschlagen. Weitere Informationen finden Sie unter [optionale Parameter](https://github.com/SignalR/SignalR/issues/324).

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a>"Firefox kann keine Verbindung mit dem Server am herstellen &lt;Adresse&gt;" Fehler in Firebug

Diese Fehlermeldung kann in Firebug angezeigt werden, wenn Aushandlung von der WebSocket-Transport fehlschlägt und einen anderen Transportmechanismus wird stattdessen verwendet. Dieses Verhalten ist vorgesehen.

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a>Fehler "Das Remotezertifikat ist laut Validierungsverfahren ungültig", in .NET Client-Anwendung

Wenn Ihr Server benutzerdefinierte-Clientzertifikate erfordert, dann können Sie ein x509certificate für die Verbindung hinzufügen, bevor die Anforderung ausgeführt wird. Fügen Sie das Zertifikat, um die Verbindung mit `Connection.AddClientCertificate`.

### <a name="connection-drops-after-authentication-times-out"></a>Verbindung getrennt wird, nach der Authentifizierung ein Timeout auftritt

Dieses Verhalten ist vorgesehen. Anmeldeinformationen für die Authentifizierung können nicht geändert werden, während eine Verbindung aktiv ist; um Anmeldeinformationen zu aktualisieren, muss die Verbindung beendet und neu gestartet werden.

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a>OnConnected wird aufgerufen, zweimal, wenn Sie mithilfe von jQuery Mobile

jQuery Mobile `initializePage` Funktion erzwingt, dass die Skripts auf jeder Seite erneut ausgeführt werden, wodurch eine zweite Verbindung. Lösungen für dieses Problem sind:

- Schließen der Verweis auf jQuery Mobile, bevor Sie Ihre JavaScript-Datei.
- Deaktivieren Sie die `initializePage` Funktion durch Festlegen von `$.mobile.autoInitializePage = false`.
- Warten Sie auf der Seite abgeschlossen wird, initialisieren vor dem Starten der Verbindung.

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a>Nachrichten werden in Silverlight-Anwendungen, die mithilfe von Server gesendete Ereignisse verzögert.

Nachrichten werden verzögert, wenn Ereignisse mit Silverlight mithilfe von Server gesendet werden. Um lange Abfragen stattdessen verwendet werden, zu erzwingen, verwenden Sie Folgendes, wenn die Verbindung ab:

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a>Mithilfe von "Berechtigung verweigert" Forever Frame-Protokoll

Dies ist ein bekanntes Problem, das beschrieben [hier](https://github.com/SignalR/SignalR/issues/1963). Dieses Symptom kann mithilfe der neuesten JQuery-Bibliothek angezeigt werden; die problemumgehung besteht darin, Ihre Anwendung in JQuery 1.8.2 herabstufen.

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a>Kompilierung und serverseitigen Fehlern

 Der folgende Abschnitt enthält mögliche Lösungen für Compiler und serverseitigen Common Language Runtime-Fehler. 

### <a name="reference-to-hub-instance-is-null"></a>Verweis auf den Hub-Instanz ist null.

Da für jede Verbindung eine hubinstanz erstellt wird, können nicht Sie in Ihrem Code selbst eine Instanz eines Hubs erstellen. Um Methoden auf einem Hub von außerhalb der Hub selbst aufrufen zu können, finden Sie unter [wie Client-Methoden aufrufen und Verwalten von Gruppen von außerhalb der hubklasse](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) zum Abrufen eines Verweises auf den hubkontext.

### <a name="httpcontextcurrentsession-is-null"></a>HTTPContext.Current.Session ist null.

Dieses Verhalten ist vorgesehen. SignalR unterstützt nicht den ASP.NET-Sitzungsstatus, da den Sitzungszustand aktivieren duplexnachrichten verletzen würden.

### <a name="no-suitable-method-to-override"></a>Keine passende Methode zum Überschreiben

Dieser Fehler kann angezeigt werden, wenn Sie Code über die ältere Dokumentation und in Blogs verwenden. Stellen Sie sicher, dass Sie keine Namen von Methoden verweisen, die als veraltet markiert oder geändert wurden (z. B. `OnConnectedAsync`).

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a>HostContextExtensions.WebSocketServerUrl is null

Dieses Verhalten ist vorgesehen. Dieser Member ist veraltet und sollte nicht verwendet werden.

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a>Fehler "eine Route mit dem Namen'signalr.hubs' ist bereits in der routenauflistung"

Dieser Fehler wird angezeigt, wenn `MapHubs` zweimal von der Anwendung aufgerufen wird. Einige Beispiel-Anwendungen rufen `MapHubs` direkt in der globalen Datei; andere nehmen Sie den Aufruf in einer Wrapperklasse. Stellen Sie sicher, dass Ihre Anwendung keine sowohl durchführt.

<a id="vs"></a>

## <a name="visual-studio-issues"></a>Probleme bei Visual Studio

Dieser Abschnitt beschreibt Probleme in Visual Studio.

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a>Knoten "Dokumente" Skript wird im Projektmappen-Explorer nicht angezeigt.

Einige unserer Tutorials führen Sie auf den Knoten "Skriptdokumente" im Projektmappen-Explorer während des Debuggens. Dieser Knoten wird durch den JavaScript-Debugger erstellt und wird nur angezeigt, während des Debuggens von Browser-Clients im Internet Explorer; der Knoten wird nicht angezeigt, wenn Chrome oder Firefox verwendet werden. Der JavaScript-Debugger wird auch nicht ausgeführt werden, wenn ein anderer Client-Debugger, wie z. B. der Silverlight-Debugger ausgeführt wird.

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a>SignalR funktioniert nicht für Visual Studio 2008 oder früher

Dieses Verhalten ist vorgesehen. SignalR erfordert .NET Framework 4 oder höher. Dies erfordert, dass es sich bei SignalR-Anwendungen in Visual Studio 2010 oder höher entwickelt werden.

<a id="iis"></a>

## <a name="iis-issues"></a>IIS-Probleme

Dieser Abschnitt enthält die Probleme mit Internet Information Services.

### <a name="web-site-crashes-after-maphubs-call"></a>Website stürzt ab, nach dem Aufruf von MapHubs

Dieses Problem wurde in der neuesten Version von SignalR behoben wurde. Stellen Sie sicher, dass Sie die neueste veröffentlichte Version von SignalR Aktualisieren Ihrer Installation mithilfe von NuGet verwenden.

<a id="azure"></a>

## <a name="azure-issues"></a>Probleme mit Azure

Dieser Abschnitt enthält die Probleme mit Microsoft Azure.

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a>Nachrichten werden nicht über die Azure-Backplane empfangen, nach dem Ändern der Themennamen

In den Themen, die von der Azure-Rückwandplatine verwendet werden sollen nicht BenutzerKonfigurierbar.
