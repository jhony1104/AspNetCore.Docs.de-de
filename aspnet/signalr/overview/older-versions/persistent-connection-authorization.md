---
uid: signalr/overview/older-versions/persistent-connection-authorization
title: Authentifizierung und Autorisierung für permanente SignalR-Verbindungen (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird. Allgemeine Informationen zum Integrieren von Sicherheit in einer SignalR-Anwendung...
ms.author: bradyg
ms.date: 10/21/2013
ms.assetid: c34bc627-41af-4c21-a817-e97a19a7f252
msc.legacyurl: /signalr/overview/older-versions/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: c4a2c9b4fa05e43ade98fb521c59f8645b43ffea
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837350"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections-signalr-1x"></a>Authentifizierung und Autorisierung für permanente SignalR-Verbindungen (SignalR 1.x)
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird. Allgemeine Informationen über die Sicherheit in einer SignalR-Anwendung integrieren, finden Sie unter [Einführung zur Sicherheit](index.md).


## <a name="enforce-authorization"></a>Erzwingen der Autorisierung

Autorisierungsregeln zu erzwingen, wenn es sich bei Verwendung einer [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) müssen Sie überschreiben die `AuthorizeRequest` Methode. Sie können keine der `Authorize` Attribut mit permanenten Verbindungen. Die `AuthorizeRequest` Methode wird aufgerufen, durch das SignalR Framework vor jeder Anforderung, um sicherzustellen, dass der Benutzer zum Ausführen der angeforderten Aktion berechtigt ist. Die `AuthorizeRequest` Methode wird nicht vom Client aufgerufen; stattdessen authentifiziert den Benutzer über Standardauthentifizierungsmechanismus Ihrer Anwendung.

Das folgende Beispiel zeigt, wie Sie Anforderungen an authentifizierte Benutzer beschränken.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Sie können eine beliebige benutzerdefinierte Autorisierungslogik in der Methode AuthorizeRequest hinzufügen; Es wird z. B. überprüft, ob ein Benutzer zu einer bestimmten Rolle gehört.
