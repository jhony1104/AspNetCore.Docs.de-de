---
uid: signalr/overview/older-versions/introduction-to-security
title: Einführung in die Sicherheit von SignalR (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Beschreibt die Sicherheitsprobleme, die Sie berücksichtigen müssen, bei der Entwicklung einer SignalR-Anwendung.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: 715a4059-d307-4631-abbb-c789c95d6eb4
msc.legacyurl: /signalr/overview/older-versions/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 94a4598595d025267c69933a03be2c164066cc98
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837805"
---
<a name="introduction-to-signalr-security-signalr-1x"></a>Einführung in die Sicherheit von SignalR (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieser Artikel beschreibt die Sicherheitsprobleme, die Sie berücksichtigen müssen, bei der Entwicklung einer SignalR-Anwendung.


## <a name="overview"></a>Übersicht

Dieses Dokument enthält folgende Abschnitte:

- [SignalR-Sicherheitskonzepte](#concepts)

    - [Authentifizierung und Autorisierung](#authentication)
    - [Verbindungstoken](#connectiontoken)
    - [Tritt von Gruppen beim Wiederherstellen der Verbindung](#rejoingroup)
- [Wie SignalR websiteübergreifende Anforderungsfälschung verhindert](#csrf)
- [Empfehlungen zur Sicherheit von SignalR](#recommendations)

    - [Secure Socket Layer (SSL)-Protokoll](#ssl)
    - [Verwenden Sie Gruppen nicht als Sicherheitsmechanismus](#groupsecurity)
    - [Sichere Verarbeitung von Eingaben von clients](#input)
    - [Eine Änderung im Status "Benutzer" mit einer aktiven Verbindung abgleichen](#reconcile)
    - [Automatisch generierte JavaScript-Proxy-Dateien](#autogen)
    - [Ausnahmen](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a>SignalR-Sicherheitskonzepte

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

SignalR ist in der vorhandenen Struktur der Authentifizierung für eine Anwendung integriert werden soll. Es bietet keine Funktionen, zum Authentifizieren von Benutzern. Stattdessen authentifizieren Sie Benutzer aus, wie Sie in Ihrer Anwendung normalerweise, und klicken Sie dann mit den Ergebnissen der Authentifizierung in Ihrem SignalR-Code arbeiten. Beispielsweise können Sie authentifizieren Sie Ihre Benutzer bei der Formularauthentifizierung von ASP.NET und dann in Ihrem Hub erzwingen, welche Benutzer oder Rollen sind berechtigt, eine Methode aufzurufen. In Ihrem Hub können Sie auch Authentifizierungsinformationen, z. B. Benutzernamen oder einer Rolle an den Client, ob ein Benutzer angehört übergeben.

SignalR stellt die [autorisieren](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) Attribut, um anzugeben, welche Benutzer mit einem Hub oder die Methode zugreifen. Sie wenden das Authorize-Attribut, auf die entweder einen Hub oder bestimmte Methoden in einem Hub. Ohne das Authorize-Attribut sind alle öffentliche Methoden des Hubs auf einem Client, der mit dem Hub verbunden ist. Weitere Informationen zu Hubs finden Sie unter [Authentifizierung und Autorisierung für SignalR-Hubs](../security/hub-authorization.md).

Die `Authorize` Attribut wird nur mit Hubs verwendet. Autorisierungsregeln zu erzwingen, wenn es sich bei Verwendung einer `PersistentConnection` müssen Sie überschreiben die `AuthorizeRequest` Methode. Weitere Informationen zu permanenten Verbindungen, finden Sie unter [Authentifizierung und Autorisierung für permanente SignalR-Verbindungen](../security/persistent-connection-authorization.md).

<a id="connectiontoken"></a>

### <a name="connection-token"></a>Verbindungstoken

SignalR verringert das Risiko böswilliger Befehle ausführt, durch die Überprüfung der Identität des Absenders. Ein Verbindungstoken, mit dem Verbindungs-Id und der Benutzername für authentifizierte Benutzer wird zwischen dem Client und Server für jede Anforderung übergeben. Die Verbindungs-Id ist ein eindeutiger Bezeichner, der durch den Server nach dem Zufallsprinzip generiert wird, wenn eine neue Verbindung wird erstellt, und für die Dauer der Verbindung gespeichert ist. Der Benutzername wird von der Authentifizierungsmechanismus für die Webanwendung bereitgestellt. Das Verbindungstoken ist durch Verschlüsselung und eine digitale Signatur geschützt.

![](introduction-to-security/_static/image2.png)

Für jede Anforderung überprüft der Server den Inhalt der Token, um sicherzustellen, dass der angegebene Benutzer die Anforderung stammt. Der Benutzername muss die Verbindungs-Id entsprechen. Durch Überprüfen sowohl die Verbindungs-Id und der Benutzername, verhindert, dass SignalR einen böswilligen Benutzer problemlos Identität eines anderen Benutzers. Die Anforderung schlägt fehl, wenn der Server kann nicht das Verbindungstoken überprüfen.

![](introduction-to-security/_static/image4.png)

Da die Verbindungs-Id Teil, bei der Überprüfung ist, sollten nicht Verbindungs-Id eines Benutzers, für andere Benutzer anzeigen oder den Wert auf dem Client, z. B. in einem Cookie gespeichert.

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a>Tritt von Gruppen beim Wiederherstellen der Verbindung

Standardmäßig wird die SignalR-Anwendung automatisch neu zuweisen eines Benutzers zu den entsprechenden Gruppen beim Wiederherstellen der Verbindung von einer vorübergehenden Störung, z. B. wenn eine Verbindung gelöscht und erneut hergestellt wird, bevor die Verbindung ein eintritt Timeout. Beim Wiederherstellen der Verbindung übergibt der Client ein Token von Gruppe, die die Verbindungs-Id und die zugewiesenen Gruppen enthält. Das Group-Token ist digital signiert und verschlüsselt. Der Client weiterhin die gleichen Verbindungs-Id hat, nachdem eine erneute Verbindung; aus diesem Grund muss die Verbindungs-Id, die von der neu verbundenen Client übergeben werden die vorherigen vom Client verwendeten Verbindungs-Id überein. Diese Überprüfung wird verhindert, dass einen böswilligen Benutzer Anforderungen, nicht autorisierten Gruppe beizutreten, beim Wiederherstellen der Verbindung zu übergeben.

Allerdings ist es wichtig zu beachten Sie, dass das Token für die Gruppe nicht abläuft. Wenn ein Benutzer zu einer Gruppe, in der Vergangenheit gehören, aber aus dieser Gruppe gesperrt wurde, dieser Benutzer möglicherweise um ein Token für die Gruppe zu imitieren, die den unzulässige Gruppe enthält. Bei Bedarf, um sicher zu verwalten, welche Benutzer welche Gruppen angehören, müssen Sie die Daten auf dem Server, z. B. in einer Datenbank zu speichern. Anschließend fügen Sie Logik Ihrer Anwendung, die auf dem Server überprüft, ob ein Benutzer zu einer Gruppe gehört. Ein Beispiel für das Überprüfen der Gruppenmitgliedschaft, finden Sie unter [arbeiten mit Gruppen](../guide-to-the-api/working-with-groups.md).

Tritt automatisch Gruppen gilt nur, wenn eine Verbindung die Verbindung nach einer vorübergehenden Störung wiederhergestellt wird. Wenn ein Benutzer durch die Verbindung trennt muss das Verlassen der Anwendung oder dem Neustart der Anwendung, die Anwendung wie den richtigen Gruppen diesen Benutzer hinzugefügt behandeln. Weitere Informationen finden Sie unter [arbeiten mit Gruppen](../guide-to-the-api/working-with-groups.md).

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a>Wie SignalR websiteübergreifende Anforderungsfälschung verhindert

Cross-Site Request Forgery (CSRF) handelt es sich um einen Angriff, in dem eine schädliche Website sendet eine Anforderung an einer anfälligen Website, in denen der Benutzer zurzeit angemeldet ist. SignalR verhindert CSRF, indem Sie machen es sehr unwahrscheinlich ist, für eine schädliche Website um eine gültige Anforderung für die SignalR-Anwendung zu erstellen.

### <a name="description-of-csrf-attack"></a>Beschreibung von CSRF-Angriffe

Hier ist ein Beispiel eines CSRF-Angriffs aus:

1. Ein Benutzer meldet sich bei `www.example.com`, mit Formularauthentifizierung.
2. Der Server authentifiziert den Benutzer. Die Antwort vom Server enthält ein Authentifizierungscookie.
3. Der Benutzer, ohne Abmeldung, eine schädliche Website besucht. Diese schädliche Websites enthält das folgende HTML-Format an: 

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   Beachten Sie, dass die Formularaktion an den Standort anfällig für nicht auf die bösartige Website sendet. Dies ist der Teil von "Cross-Site" für CSRF.
4. Der Benutzer klickt auf die Schaltfläche "Senden". Der Browser enthält das Cookie für die Authentifizierung mit der Anforderung.
5. Die Anforderung ausgeführt wird, auf dem Server "Beispiel.com" mit dem Kontext des Benutzers Authentifizierung und Aktionen möglich, die ein authentifizierter Benutzer ausführen darf.

Obwohl in diesem Beispiel wird den Benutzer auf die Schaltfläche "Format" erforderlich sind, könnten auch einfach ein Skript ausführen, die eine AJAX-Anforderung an Ihre SignalR-Anwendung sendet die schädliche Seite. Darüber hinaus verhindert mithilfe von SSL ein CSRF-Angriffs nicht, da die bösartige Website eine "https://"-Anforderung senden kann.

In der Regel sind die CSRF-Angriffe möglich vor Websites, die Cookies für die Authentifizierung zu verwenden, da Browser alle relevanten Cookies an die Ziel-Website senden. CSRF-Angriffe sind jedoch nicht beschränkt auf Cookies ausnutzen. Z. B. Basis-und Digestauthentifizierung sind auch anfällig. Nachdem ein Benutzer mit Basic oder Digest-Authentifizierung anmeldet, sendet der Browser automatisch die Anmeldeinformationen an, bis die Sitzung beendet.

### <a name="csrf-mitigations-taken-by-signalr"></a>CSRF-Lösungen, die von SignalR

SignalR verwendet die folgenden Schritte aus, um zu verhindern, dass eine schädliche Website gültige Anforderungen für Ihre SignalR-Anwendung erstellen. Diese Schritte, die standardmäßig erstellt werden und erfordern keine Maßnahmen in Ihrem Code.

- **Deaktivieren Sie die domänenübergreifende Anforderungen**  
 Standardmäßig sind domänenübergreifende Anforderungen in einer SignalR-Anwendung, um zu verhindern, dass Benutzer Aufrufen von einem SignalR-Endpunkt aus einer externen Domäne deaktiviert. Jede Anforderung, die aus einer externen Domäne stammen, wird automatisch als ungültig angesehen und ist blockiert. Es wird empfohlen, dass Sie dieses Standardverhalten beibehalten. Andernfalls kann eine schädliche Website Benutzer bringen, Senden von Befehlen auf Ihrer Website. Zu domänenübergreifende Anforderungen verwenden, finden Sie unter [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .
- **Übergeben Sie Verbindungstoken Abfragezeichenfolge nicht cookie**  
 SignalR übergibt das Verbindungstoken als Wert einer Abfragezeichenfolge, statt als Cookie. Durch das Verbindungstoken wird nicht als ein Cookie speichern, wird das Verbindungstoken nicht versehentlich vom Browser weitergeleitet, wenn Schadsoftware erkannt wird. Darüber hinaus wird das Verbindungstoken nicht über die aktuelle Verbindung beibehalten. Aus diesem Grund kann kein böswilliger Benutzer unter der Authentifizierungsinformationen eines anderen Benutzers anfordern.
- **Verbindungstoken überprüfen**  
 Siehe die [Verbindungstoken](#connectiontoken) Abschnitt, der Server weiß, welche Verbindungs-Id jeder authentifizierte Benutzer zugeordnet ist. Der Server verarbeitet keine Anforderungen über eine Verbindungs-Id, die nicht den Benutzernamen übereinstimmen. Es ist unwahrscheinlich, dass ein böswilliger Benutzer eine gültige Anforderung erraten kann, da der böswillige Benutzer den Benutzernamen und die aktuelle nach dem Zufallsprinzip generierte Verbindungs­ID kennen muss. Dieser Verbindungs-Id wird ungültig, sobald die Verbindung beendet wird. Anonyme Benutzer müssen nicht den Zugriff auf vertrauliche Informationen.

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>Empfehlungen zur Sicherheit von SignalR

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>Secure Socket Layer (SSL)-Protokoll

Das SSL-Protokoll verwendet Verschlüsselung, um die Übertragung von Daten zwischen einem Client und Server zu sichern. Falls Ihre SignalR-Anwendung vertrauliche Informationen zwischen Client und Server sendet, verwenden Sie SSL für den Transport. Weitere Informationen zum Einrichten von SSL finden Sie unter [So richten Sie SSL in IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>Verwenden Sie Gruppen nicht als Sicherheitsmechanismus

Gruppen sind eine einfache Möglichkeit im Zusammenhang stehenden Benutzer erfassen, aber sie sind sich nicht um einen sicheren Mechanismus zum Einschränken des Zugriffs auf vertrauliche Informationen. Dies gilt insbesondere, wenn der Benutzer automatisch Gruppen während einer erneut hergestellten Verbindung erneut hinzuzufügen können. Stattdessen Sie werden berechtigte Benutzer zu einer Rolle hinzufügen, und beschränken des Zugriffs auf eine hubmethode, um nur die Mitglieder dieser Rolle. Ein Beispiel für das Einschränken des Zugriffs basierend auf einer Rolle, finden Sie unter [Authentifizierung und Autorisierung für SignalR-Hubs](../security/hub-authorization.md). Ein Beispiel für die Überprüfung der Benutzerzugriff auf Gruppen beim Wiederherstellen der Verbindung, finden Sie unter [arbeiten mit Gruppen](../guide-to-the-api/working-with-groups.md).

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>Sichere Verarbeitung von Eingaben von clients

Alle Eingaben von Clients, die eine Übertragung an andere Clients vorgesehen ist muss codiert werden, um sicherzustellen, dass ein böswilliger Benutzer keine Skripts für andere Benutzer sendet. Es empfiehlt sich, auf dem Server, statt die empfangende Clients-Nachrichten zu codieren, da Ihre SignalR-Anwendung kann viele verschiedene Arten von Clients möglicherweise. Aus diesem Grund funktioniert die HTML-Codierung für eine Webclient, jedoch nicht für andere Typen von Clients. Z. B. eine Web-Client-Methode, um eine Chatnachricht anzuzeigen würde sicher behandeln, den Benutzernamen und die Nachricht durch den Aufruf der `html()` Funktion.

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a>Eine Änderung im Status "Benutzer" mit einer aktiven Verbindung abgleichen

Wenn die Authentication-Status eines Benutzers ändert, während eine aktive Verbindung vorhanden ist, der Benutzer wird eine Fehlermeldung, aus der hervorgeht, "die Identität des Benutzers kann nicht während einer aktiven SignalR-Verbindungs ändern". In diesem Fall die Anwendung erneut eine Verbindung herstellen soll an den Server aus, um sicherzustellen, dass der Verbindungs-Id und dem Benutzernamen koordiniert werden. Z. B. Wenn Ihre Anwendung kann der Benutzer abmelden, während eine aktive Verbindung vorhanden ist, wird der Benutzername für die Verbindung nicht mehr übereinstimmen den Namen, der für die nächste Anforderung übergeben wird. Sie möchten, die Verbindung zu beenden, bevor der Benutzer abmeldet, und dann neu gestartet werden.

Allerdings ist es wichtig zu beachten, dass die meisten Anwendungen müssen nicht manuell beenden und starten die Verbindung. Wenn die Anwendung Benutzer auf eine separate Seite nach der Anmeldung, z. B. das Standardverhalten in einer Web Forms-Anwendung oder eine MVC-Anwendung leitet oder die aktuelle Seite aktualisiert nach dem Abmelden, wird automatisch getrennt und nicht die aktive Verbindung zusätzliche Aktion erforderlich.

Das folgende Beispiel zeigt, wie zum Beenden und starten eine Verbindung aus, wenn sich der Benutzerstatus geändert hat.

[!code-html[Main](introduction-to-security/samples/sample3.html)]

Oder der Status des Benutzers Authentifizierung möglicherweise ändern, wenn Ihre Website gleitenden Ablauf bei der Formularauthentifizierung verwendet aus, und keine Aktivität auf das Authentifizierungscookie gültig bleiben. In diesem Fall der Benutzer werden abgemeldet, und der Benutzername wird nicht mehr den Benutzernamen in das Verbindungstoken übereinstimmen. Sie können dieses Problem beheben, indem Sie ein Skript, das eine Ressource auf dem Webserver, auf das Authentifizierungscookie gültig halten in regelmäßigen Abständen Anforderungen hinzufügen. Das folgende Beispiel zeigt, wie Sie eine Ressource alle 30 Minuten anfordern.

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a>Automatisch generierte JavaScript-Proxy-Dateien

Wenn Sie nicht alle Hubs und Methoden in der JavaScript-Proxy-Datei für jeden Benutzer einschließen möchten, können Sie die automatische Generierung der Datei deaktivieren. Sie können diese Option auswählen, wenn Sie mehrere Hubs und Methoden, aber nicht jeder Benutzer alle Methoden berücksichtigen möchten. Deaktivieren Sie automatische Generierung, indem **EnableJavaScriptProxies** zu **"false"**.

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

Weitere Informationen zu den JavaScript-Proxy-Dateien finden Sie unter [des generierten Proxys und was dies für Sie übernimmt](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy). <a id="exceptions"></a>

### <a name="exceptions"></a>Ausnahmen

Vermeiden Sie die Exception-Objekte für Clients übergeben werden, da die Objekte für vertrauliche Informationen zu den Clients zur Verfügung stellen können. Rufen Sie stattdessen eine Methode, auf dem Client, der die entsprechende Fehlermeldung angezeigt.

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]
