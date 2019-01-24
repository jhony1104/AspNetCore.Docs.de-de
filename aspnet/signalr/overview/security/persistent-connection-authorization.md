---
uid: signalr/overview/security/persistent-connection-authorization
title: Authentifizierung und Autorisierung für permanente SignalR-Verbindungen | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird. Allgemeine Informationen zum Integrieren von Sicherheit in einer SignalR-Anwendung...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835621"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a>Authentifizierung und Autorisierung für permanente SignalR-Verbindungen
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird. Allgemeine Informationen über die Sicherheit in einer SignalR-Anwendung integrieren, finden Sie unter [Einführung zur Sicherheit](introduction-to-security.md).
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


## <a name="enforce-authorization"></a>Erzwingen der Autorisierung

Autorisierungsregeln zu erzwingen, wenn es sich bei Verwendung einer [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) müssen Sie überschreiben die `AuthorizeRequest` Methode. Sie können keine der `Authorize` Attribut mit permanenten Verbindungen. Die `AuthorizeRequest` Methode wird aufgerufen, durch das SignalR Framework vor jeder Anforderung, um sicherzustellen, dass der Benutzer zum Ausführen der angeforderten Aktion berechtigt ist. Die `AuthorizeRequest` Methode wird nicht vom Client aufgerufen; stattdessen authentifiziert den Benutzer über Standardauthentifizierungsmechanismus Ihrer Anwendung.

Das folgende Beispiel zeigt, wie Sie Anforderungen an authentifizierte Benutzer beschränken.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Sie können eine beliebige benutzerdefinierte Autorisierungslogik in der Methode AuthorizeRequest hinzufügen; Es wird z. B. überprüft, ob ein Benutzer zu einer bestimmten Rolle gehört.
