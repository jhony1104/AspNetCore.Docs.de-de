---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie die ASP.NET Core SignalR-Bibliothek vereinfacht das Hinzufügen von Echtzeitfunktionalität für apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836362"
---
# <a name="introduction-to-aspnet-core-signalr"></a>Einführung in ASP.NET Core SignalR

## <a name="what-is-signalr"></a>Was ist SignalR?

ASP.NET Core SignalR handelt es sich um ein Open Source-Bibliothek, die Hinzufügen von echtzeitwebfunktionalität zu apps vereinfacht. Echtzeit-Webfunktionen ermöglicht serverseitiger Code Inhalt per Pushvorgang an Clients sofort an.

Gute Kandidaten für SignalR:

* Apps, die eine hohe Frequenz von Updates auf dem Server erfordern. Beispiele sind Gaming, soziale Netzwerke, voting, Auktion, Karten und GPS-apps.
* Dashboards und überwachungs-apps. Beispiele sind Unternehmensdashboards, sofortupdates von Verkaufszahlen oder reisehinweise.
* Apps für die Zusammenarbeit. Whiteboard-apps und Software für teambesprechungen sind Beispiele von apps für die Zusammenarbeit.
* Apps, die Benachrichtigungen benötigt. Verwenden Benachrichtigungen, soziale Netzwerke, e-Mail, Chat, Games, reisehinweise und viele andere apps.

SignalR stellt eine API zum Erstellen von Server-zu-Client [Remoteprozeduraufrufe (RPC)](https://wikipedia.org/wiki/Remote_procedure_call). Die RPCs rufen JavaScript-Funktionen auf Clients von serverseitigen .NET Core-Code.

Hier sind einige Features von SignalR für ASP.NET Core:

* Verbindungsverwaltung behandelt automatisch.
* Sendet Nachrichten gleichzeitig an alle verbundenen Clients. Z. B. einem Chatraum.
* Sendet Nachrichten an bestimmte Clients oder Clientgruppen.
* Skaliert, um Datenverkehr zu verarbeiten.

Die Quelle befindet sich einem [SignalR-Repository auf GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Transportprotokolle

SignalR unterstützt verschiedene Techniken für die Behandlung von Echtzeitkommunikation:

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Vom Server gesendeten Ereignisse
* Lange Abrufvorgänge

SignalR wählt automatisch die beste Transportmethode, die innerhalb der Funktionen von Server und Client liegt.

## <a name="hubs"></a>Hubs

SignalR verwendet *Hubs* für die Kommunikation zwischen Clients und Servern.

Ein Hub ist eine allgemeine Pipeline, die ermöglicht einem Client und Server, zum Aufrufen von Methoden voneinander. SignalR übernimmt die Verteilung über Computergrenzen hinweg automatisch, sodass Clients zum Aufrufen von Methoden, die auf dem Server und umgekehrt. Sie können Methoden, stark typisierten Parametern übergeben, um die modellbindung aktiviert. SignalR enthält zwei integrierte Hub-Protokolle: ein Text-Protokoll, anhand von JSON- und ein binäres Protokoll, das basierend auf [MessagePack](https://msgpack.org/).  MessagePack erstellt in der Regel über kleinere Nachrichten, die im Vergleich zu JSON. Müssen ältere Browser unterstützen [XHR-Ebene 2](https://caniuse.com/#feat=xhr2) MessagePack protokollunterstützung bereitstellen.

Hubs Aufrufen der clientseitige Code durch Senden von Nachrichten, die den Namen und die Parameter der Methode der clientseitigen enthalten. Objekte, die als Parameter gesendet werden deserialisiert das konfigurierte Protokoll verwenden. Der Client versucht, die mit dem Namen einer Methode im clientseitigen Code übereinstimmen. Wenn der Client eine Übereinstimmung findet, die Methode aufruft und die deserialisierte Parameterdaten übergeben.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte mit SignalR für ASP.NET Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
