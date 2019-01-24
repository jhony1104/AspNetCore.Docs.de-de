---
uid: signalr/overview/older-versions/hub-authorization
title: Authentifizierung und Autorisierung für SignalR-Hubs (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Dieses Thema beschreibt, wie Sie einschränken, welche Benutzer oder Rollen hubmethoden zugreifen können.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: 3d2dfc0e-eac2-4076-a468-325d3d01cc7b
msc.legacyurl: /signalr/overview/older-versions/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7f4a76109111f19dc4381ad01e642afdabade336
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836898"
---
<a name="authentication-and-authorization-for-signalr-hubs-signalr-1x"></a>Authentifizierung und Autorisierung für SignalR-Hubs (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Thema beschreibt, wie Sie einschränken, welche Benutzer oder Rollen hubmethoden zugreifen können.


## <a name="overview"></a>Übersicht

Dieses Thema enthält folgende Abschnitte:

- [Autorisieren von Attribut](#authorizeattribute)
- [Erzwingen der Authentifizierung für alle hubs](#requireauth)
- [Benutzerdefinierte Autorisierung](#custom)
- [Übergeben von Informationen für die Authentifizierung für clients](#passauth)
- [Authentifizierungsoptionen für .NET-Clients](#authoptions)

    - [Cookie mit der Formularauthentifizierung](#cookie)
    - [Windows-Authentifizierung](#windows)
    - [Connection-header](#header)
    - [Zertifikat](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a>Autorisieren von Attribut

SignalR stellt die [autorisieren](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) Attribut, um anzugeben, welche Benutzer oder Rollen mit einem Hub oder die Methode zugreifen können. Dieses Attribut befindet sich in der `Microsoft.AspNet.SignalR` Namespace. Sie wenden die `Authorize` Attribut entweder einen Hub oder bestimmte Methoden in einem Hub. Beim Anwenden der `Authorize` Attribut, um eine hubklasse, die angegebene autorisierungsanforderung gilt für alle Methoden in den Hub. Die verschiedenen Typen von autorisierungsanforderungen, die Sie anwenden können, sind unten dargestellt. Ohne die `Authorize` -Attribut, die alle öffentliche Methoden für den Hub werden auf einem Client, der mit dem Hub verbunden ist.

Wenn Sie eine Rolle mit dem Namen "Administrator" in Ihrer Webanwendung definiert haben, können Sie angeben, dass nur Benutzer in dieser Rolle einen Hub mit den folgenden Code zugreifen können.

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

Sie können auch angeben, dass ein Hub enthält eine Methode, die für alle Benutzer verfügbar ist, und eine zweite Methode, die nur für authentifizierte Benutzer verfügbar ist, wie unten dargestellt.

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

Die folgenden Beispiele beziehen sich auf verschiedenen autorisierungsszenarien:

- `[Authorize]` – nur authentifizierte Benutzer
- `[Authorize(Roles = "Admin,Manager")]` – nur authentifizierte Benutzer in den angegebenen Rollen
- `[Authorize(Users = "user1,user2")]` – nur authentifizierte Benutzer mit den angegebenen Benutzernamen
- `[Authorize(RequireOutgoing=false)]` – nur authentifizierte Benutzer können den Hub aufrufen, aber die Aufrufe vom Server an Clients keine Autorisierung, z. B., Beschränkung Wenn nur bestimmte Benutzer eine Nachricht senden können, aber alle anderen können die Nachricht empfangen. Die RequireOutgoing-Eigenschaft kann nur für die gesamte Hub-Instanz nicht für Einzelpersonen-Methoden in den Hub angewendet werden. Wenn es sich bei RequireOutgoing nicht auf "false" festgelegt ist, werden nur Benutzer, die die autorisierungsanforderung erfüllen vom Server aufgerufen.

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a>Erzwingen der Authentifizierung für alle hubs

Sie können erzwingen der Authentifizierung für alle Hubs und hubmethoden in Ihrer Anwendung durch Aufrufen der [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) Methode, wenn die Anwendung gestartet wird. Sie können diese Methode verwenden, wenn Sie mehrere Hubs haben und eine Anforderung zur Authentifizierung für alle erzwungen werden sollen. Mit dieser Methode können keine Sie-Serverrolle, Benutzer oder ausgehende Autorisierung angeben. Sie können nur angeben, dass der Zugriff auf die hubmethoden auf authentifizierte Benutzer beschränkt ist. Allerdings können Sie weiterhin das Authorize-Attribut, Hubs oder Methoden ein, geben Sie zusätzliche Anforderungen anwenden. Alle Anforderungen, die Sie in Attributen angeben, wird neben der Authentifizierung die grundlegende Anforderung angewendet.

Das folgende Beispiel zeigt eine Datei "Global.asax" das alle Methoden des Hubs auf authentifizierte Benutzer beschränkt.

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

Aufrufen der `RequireAuthentication()` -Methode, nach der Verarbeitung einer Anforderung SignalR, SignalR löst eine `InvalidOperationException` Ausnahme. Diese Ausnahme wird ausgelöst, da Sie ein Modul auf dem Hubpipeline-Objekt hinzufügen können, nachdem die Pipeline aufgerufen wurde. Im vorherige Beispiel zeigt den Aufruf der `RequireAuthentication` -Methode in der die `Application_Start` Methode, die einmal vor der Verarbeitung der ersten Anforderung ausgeführt wird.

<a id="custom"></a>

## <a name="customized-authorization"></a>Benutzerdefinierte Autorisierung

Bei Bedarf anpassen, wie die Autorisierung bestimmt wird, können Sie eine abgeleitete Klasse erstellen `AuthorizeAttribute` und überschreiben die [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) Methode. Diese Methode wird aufgerufen, für jede Anforderung zu bestimmen, ob der Benutzer autorisiert ist, um die Anforderung abzuschließen. In der überschriebenen Methode geben Sie die erforderliche Logik an, für Ihr Szenario für die Autorisierung. Das folgende Beispiel zeigt, wie Sie das Erzwingen der Autorisierung über anspruchsbasierte Identität.

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a>Übergeben von Informationen für die Authentifizierung für clients

Sie müssen möglicherweise Informationen für die Authentifizierung im Code verwenden, die auf dem Client ausgeführt wird. Sie übergeben die erforderliche Informationen beim Aufruf der Methoden auf dem Client. Beispielsweise könnte eine Chat-Anwendung-Methode als Parameter der Benutzername der Person, die eine Nachricht, übergeben wie unten dargestellt.

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

Oder Sie können ein Objekt, um die Authentifizierungsinformationen darstellen, und übermitteln Sie dieses Objekt als Parameter erstellen, wie unten dargestellt.

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

Sie sollten niemals eine Clientverbindungs-Id für andere Clients übergeben, wie ein böswilliger Benutzer es verwenden kann, um eine Anforderung von diesem Client imitieren.

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a>Authentifizierungsoptionen für .NET-Clients

Wenn Sie einen .NET Client, z. B. eine Konsolen-app, die mit einem Hub interagiert, die verfügen authentifizierte Benutzer beschränkt ist. folgende Aktionen ausführen, können Sie die Anmeldeinformationen in ein Cookie, der Connection-Header oder ein Zertifikat für die Authentifizierung übergeben. In die Beispielen in diesem Abschnitt zeigen, wie die verschiedenen Methoden zum Authentifizieren eines Benutzers verwendet wird. Sie sind nicht voll funktionsfähigen SignalR-apps. Weitere Informationen zu mit SignalR .NET-Client zu erhalten, finden Sie unter [-API-Leitfaden für die Hubs - Client für .NET](../guide-to-the-api/hubs-api-guide-net-client.md).

<a id="cookie"></a>

### <a name="cookie"></a>Cookie

Wenn der .NET Client mit einem Hub, der ASP.NET-Formularauthentifizierung verwendet interagiert, müssen Sie manuell das Authentifizierungscookie für die Verbindung festgelegt. Sie Cookies, das Hinzufügen der `CookieContainer` Eigenschaft für die [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) Objekt. Das folgende Beispiel zeigt eine Konsolen-app, die Ruft ein Authentifizierungscookie auf einer Webseite ab und fügt das Cookie für die Verbindung. Die URL `https://www.contoso.com/RemoteLogin` im Beispiel zeigt auf eine Webseite, die Sie erstellen müssen. Die Seite würde abrufen, den bereitgestellten Benutzernamen und das Kennwort, und versuchen, den Benutzer mit den Anmeldeinformationen anzumelden.

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

Die Konsolen-app sendet die Anmeldeinformationen für www.contoso.com/RemoteLogin die auf eine leere Seite verweisen kann, die den folgenden Code-Behind-Datei enthält.

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a>Windows-Authentifizierung

Bei Verwendung der Windows-Authentifizierung können Sie die Anmeldeinformationen des aktuellen Benutzers übergeben, indem Sie mit der [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) Eigenschaft. Sie haben die Anmeldeinformationen für die Verbindung auf den Wert des der DefaultCredentials festlegen.

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a>Connection-header

Wenn Ihre Anwendung keine Cookies verwendet wird, können Sie Benutzerinformationen in der Connection-Header übergeben. Beispielsweise können Sie ein Token in der Connection-Header übergeben.

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

Klicken Sie dann würden Sie auf dem Hub das Token des Benutzers überprüfen.

<a id="certificate"></a>

### <a name="certificate"></a>Zertifikat

Sie können ein Clientzertifikat, um zu überprüfen, ob den Benutzer übergeben. Sie fügen Sie das Zertifikat beim Erstellen der Verbindung hinzu. Das folgende Beispiel zeigt, wie nur die Verbindung ein Clientzertifikat hinzugefügt wird; die vollständige Konsolen-app werden nicht angezeigt. Er verwendet den [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) Klasse bietet mehrere Möglichkeiten zum Erstellen des Zertifikats.

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
