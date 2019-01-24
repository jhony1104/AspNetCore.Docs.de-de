---
uid: signalr/overview/security/introduction-to-security
title: Einführung in die SignalR-Sicherheit | Microsoft-Dokumentation
author: bradygaster
description: Beschreibt die Sicherheitsprobleme, die Sie berücksichtigen müssen, bei der Entwicklung einer SignalR-Anwendung.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: ed562717-8591-4936-8e10-c7e63dcb570a
msc.legacyurl: /signalr/overview/security/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 311bfb4279b1c919fddcef2c0aed657083f9c34f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837324"
---
<a name="introduction-to-signalr-security"></a>Einführung in die Sicherheit von SignalR
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieser Artikel beschreibt die Sicherheitsprobleme, die Sie berücksichtigen müssen, bei der Entwicklung einer SignalR-Anwendung.
>
> ## <a name="software-versions-used-in-this-topic"></a>In diesem Thema verwendeten Softwareversionen
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
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

SignalR stellt keine Funktionen bereit, zum Authentifizieren von Benutzern. Integrieren Sie stattdessen die SignalR-Funktionen, in der vorhandenen Struktur der Authentifizierung für eine Anwendung. Sie können Benutzer authentifizieren, wie Sie normalerweise in der Anwendung und die Arbeit mit den Ergebnissen der Authentifizierung in Ihrer SignalR würden. Beispielsweise können Sie authentifizieren Sie Ihre Benutzer bei der Formularauthentifizierung von ASP.NET und dann in Ihrem Hub erzwingen, welche Benutzer oder Rollen sind berechtigt, eine Methode aufzurufen. In Ihrem Hub können Sie auch Authentifizierungsinformationen, z. B. Benutzernamen oder einer Rolle an den Client, ob ein Benutzer angehört übergeben.

SignalR stellt die [autorisieren](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) Attribut, um anzugeben, welche Benutzer mit einem Hub oder die Methode zugreifen. Sie wenden das Authorize-Attribut, auf die entweder einen Hub oder bestimmte Methoden in einem Hub. Ohne das Authorize-Attribut sind alle öffentliche Methoden des Hubs auf einem Client, der mit dem Hub verbunden ist. Weitere Informationen zu Hubs finden Sie unter [Authentifizierung und Autorisierung für SignalR-Hubs](hub-authorization.md).

Sie übernehmen die `Authorize` Attribut Hubs allerdings nicht permanenten Verbindungen. Autorisierungsregeln zu erzwingen, wenn es sich bei Verwendung einer `PersistentConnection` müssen Sie überschreiben die `AuthorizeRequest` Methode. Weitere Informationen zu permanenten Verbindungen, finden Sie unter [Authentifizierung und Autorisierung für permanente SignalR-Verbindungen](persistent-connection-authorization.md).

<a id="connectiontoken"></a>

### <a name="connection-token"></a>Verbindungstoken

SignalR verringert das Risiko böswilliger Befehle ausführt, durch die Überprüfung der Identität des Absenders. Für jede Anforderung übergeben dem Client und Server ein Verbindungstoken, die die Verbindungs-Id und den Benutzernamen für authentifizierte Benutzer enthält. Die Verbindungs-Id identifiziert eindeutig einzelnen verbundenen Client. Der Server generiert nach dem Zufallsprinzip die Verbindungs-Id auf, wenn eine neue Verbindung erstellt wird, und die Id für die Dauer der Verbindung besteht. Der Authentifizierungsmechanismus für die Webanwendung gibt den Benutzernamen ein. SignalR verwendet verschlüsselungs- und eine digitale Signatur, um das Verbindungstoken zu schützen.

![](introduction-to-security/_static/image2.png)

Für jede Anforderung überprüft der Server den Inhalt der Token, um sicherzustellen, dass der angegebene Benutzer die Anforderung stammt. Der Benutzername muss die Verbindungs-Id entsprechen. Durch Überprüfen sowohl die Verbindungs-Id und der Benutzername, verhindert, dass SignalR einen böswilligen Benutzer problemlos Identität eines anderen Benutzers. Die Anforderung schlägt fehl, wenn der Server kann nicht das Verbindungstoken überprüfen.

![](introduction-to-security/_static/image4.png)

Da die Verbindungs-Id Teil, bei der Überprüfung ist, sollten nicht Verbindungs-Id eines Benutzers, für andere Benutzer anzeigen oder den Wert auf dem Client, z. B. in einem Cookie gespeichert.

#### <a name="connection-tokens-vs-other-token-types"></a>Verbindungs-Token im Vergleich zu anderen Typen von Sicherheitstoken

Verbindung Token werden gelegentlich von Sicherheitstools gekennzeichnet, da sie anscheinend Sitzungstoken oder Ihrer Authentifizierungstoken, die ein Risiko birgt, wenn es sich bei verfügbar gemacht werden.

Token für SignalR Verbindung kein Authentifizierungstoken. Es wird verwendet, um zu bestätigen, dass der Benutzer die Anforderung gesendet hat die gleiche, das die Verbindung erstellt haben. Das Verbindungstoken ist erforderlich, da ASP.NET SignalR-Verbindungen zwischen Servern verschieben zulässt. Das Token ordnet die Verbindung mit einem bestimmten Benutzer jedoch nicht die Identität des Benutzers an, der die Anforderung bestätigen. Eine SignalR-Anforderung ordnungsgemäß authentifiziert werden muss sie haben einige andere Token, das die Identität des Benutzers, z.B. ein Cookie bestätigt oder Bearer-Token. Aber die Verbindung token selbst wird kein Anspruch, der vom Benutzer angefordert wurde, das die Verbindungs-ID im Token enthalten, nur diesem Benutzer zugeordnet ist.

Da das Verbindungstoken keine eigene authentifizierungsanspruch bereitstellt, ist nicht davon ausgegangen, eine "Sitzung" oder "Authentication" token. Nehmen Verbindung eines bestimmten Benutzers token, und es in einer Anforderung als ein anderer Benutzer authentifiziert (oder eine nicht authentifizierte Anforderung) wiedergeben schlägt fehl, da die Identität des Benutzers der Anforderung und die Identität in das Token stimmt jedoch nicht überein.

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

1. Ein Benutzer sich anmeldet www.example.com, Formularauthentifizierung verwenden.
2. Der Server authentifiziert den Benutzer. Die Antwort vom Server enthält ein Authentifizierungscookie.
3. Der Benutzer, ohne Abmeldung, eine schädliche Website besucht. Diese schädliche Websites enthält das folgende HTML-Format an:

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   Beachten Sie, dass die Formularaktion an den Standort anfällig für nicht auf die bösartige Website sendet. Dies ist der Teil von "Cross-Site" für CSRF.
4. Der Benutzer klickt auf die Schaltfläche "Senden". Der Browser enthält das Cookie für die Authentifizierung mit der Anforderung.
5. Die Anforderung ausgeführt wird, auf dem Server "Beispiel.com" mit dem Kontext des Benutzers Authentifizierung und Aktionen möglich, die ein authentifizierter Benutzer ausführen darf.

Obwohl in diesem Beispiel wird den Benutzer auf die Schaltfläche "Format" erforderlich sind, könnten auch einfach ein Skript ausführen, die eine AJAX-Anforderung an Ihre SignalR-Anwendung sendet die schädliche Seite. Darüber hinaus verhindert mithilfe von SSL ein CSRF-Angriffs nicht, da die bösartige Website eine "https://"-Anforderung senden kann.

In der Regel sind die CSRF-Angriffe möglich vor Websites, die Cookies für die Authentifizierung zu verwenden, da Browser alle relevanten Cookies an die Ziel-Website senden. CSRF-Angriffe sind jedoch nicht beschränkt auf Cookies ausnutzen. Z. B. Basis-und Digestauthentifizierung sind auch anfällig. Nachdem ein Benutzer mit Basic oder Digest-Authentifizierung anmeldet, sendet der Browser automatisch die Anmeldeinformationen an, bis die Sitzung beendet.

### <a name="csrf-mitigations-taken-by-signalr"></a>CSRF-Lösungen, die von SignalR

SignalR verwendet die folgenden Schritte aus, um zu verhindern, dass eine schädliche Website gültige Anforderungen Ihrer Anwendung zu erstellen. SignalR verwendet die folgenden Schritte aus, in der Standardeinstellung, Sie müssen nicht in Ihrem Code keine Maßnahmen ergreifen.

- **Deaktivieren Sie die domänenübergreifende Anforderungen** SignalR deaktiviert die domänenübergreifende Anforderungen, um zu verhindern, dass Benutzer ein SignalR-Endpunkt aus einer externen Domäne aufgerufen. SignalR jede Anforderung von einer externen Domäne als ungültig betrachtet, und die Anforderung blockiert. Es wird empfohlen, dass Sie dieses Standardverhalten beibehalten; Andernfalls kann eine schädliche Website Benutzer bringen, Senden von Befehlen auf Ihrer Website. Zu domänenübergreifende Anforderungen verwenden, finden Sie unter [Gewusst wie: Herstellen einer Verbindung domänenübergreifende](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .
- **Abfragezeichenfolge nicht Cookie Verbindungstoken übergebe** SignalR übergibt das Verbindungstoken als Wert einer Abfragezeichenfolge, statt als Cookie. Das Verbindungstoken in einem Cookie speichern ist unsicher, da der Browser das Verbindungstoken versehentlich weiterleiten kann, wenn Schadsoftware erkannt wird. Darüber hinaus verhindert, dass das Verbindungstoken in der Abfragezeichenfolge übergeben das Verbindungstoken außerhalb der aktuellen Verbindungs beibehalten. Aus diesem Grund kann kein böswilliger Benutzer unter der Authentifizierungsinformationen eines anderen Benutzers anfordern.
- **Stellen Sie sicher Verbindungstoken** wie in beschrieben die [Verbindungstoken](#connectiontoken) Abschnitt, der Server weiß, welche Verbindungs-Id jeder authentifizierte Benutzer zugeordnet ist. Der Server verarbeitet keine Anforderungen über eine Verbindungs-Id, die nicht den Benutzernamen übereinstimmen. Es ist unwahrscheinlich, dass ein böswilliger Benutzer eine gültige Anforderung erraten kann, da der böswillige Benutzer den Benutzernamen und die aktuelle nach dem Zufallsprinzip generierte Verbindungs­ID kennen muss. Dieser Verbindungs-Id wird ungültig, sobald die Verbindung beendet wird. Anonyme Benutzer müssen nicht den Zugriff auf vertrauliche Informationen.

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>Empfehlungen zur Sicherheit von SignalR

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>Secure Socket Layer (SSL)-Protokoll

Das SSL-Protokoll verwendet Verschlüsselung, um die Übertragung von Daten zwischen einem Client und Server zu sichern. Falls Ihre SignalR-Anwendung vertrauliche Informationen zwischen Client und Server sendet, verwenden Sie SSL für den Transport. Weitere Informationen zum Einrichten von SSL finden Sie unter [So richten Sie SSL in IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>Verwenden Sie Gruppen nicht als Sicherheitsmechanismus

Gruppen sind eine einfache Möglichkeit im Zusammenhang stehenden Benutzer erfassen, aber sie sind sich nicht um einen sicheren Mechanismus zum Einschränken des Zugriffs auf vertrauliche Informationen. Dies gilt insbesondere, wenn der Benutzer automatisch Gruppen während einer erneut hergestellten Verbindung erneut hinzuzufügen können. Stattdessen Sie werden berechtigte Benutzer zu einer Rolle hinzufügen, und beschränken des Zugriffs auf eine hubmethode, um nur die Mitglieder dieser Rolle. Ein Beispiel für das Einschränken des Zugriffs basierend auf einer Rolle, finden Sie unter [Authentifizierung und Autorisierung für SignalR-Hubs](hub-authorization.md). Ein Beispiel für die Überprüfung der Benutzerzugriff auf Gruppen beim Wiederherstellen der Verbindung, finden Sie unter [arbeiten mit Gruppen](../guide-to-the-api/working-with-groups.md).

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>Sichere Verarbeitung von Eingaben von clients

Um sicherzustellen, dass ein böswilliger Benutzer Skript nicht für andere Benutzer gesendet wird, müssen Sie alle Eingaben von den Clients codieren, die eine Übertragung an andere Clients vorgesehen ist. Sie sollten die Nachrichten auf dem Server, statt die empfangende Clients codieren, da Ihre SignalR-Anwendung kann viele verschiedene Arten von Clients möglicherweise. Aus diesem Grund funktioniert die HTML-Codierung für eine Webclient, jedoch nicht für andere Typen von Clients. Z. B. eine Web-Client-Methode, um eine Chatnachricht anzuzeigen würde sicher behandeln, den Benutzernamen und die Nachricht durch den Aufruf der `html()` Funktion.

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
