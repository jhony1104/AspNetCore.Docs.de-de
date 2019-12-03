---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie die ASP.net Core SignalR Bibliothek das Hinzufügen von Echtzeitfunktionen zu apps vereinfacht.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: e84dd0d086cbfc80a80bc10baa33979da9b5d137
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717233"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>Einführung in ASP.NET Core SignalR

## <a name="what-is-opno-locsignalr"></a>Was ist SignalR?

Bei ASP.net Core SignalR handelt es sich um eine Open-Source-Bibliothek, die das Hinzufügen von Echt Zeit Webfunktionen zu apps vereinfacht. Die Echt Zeit webolle ermöglicht es dem serverseitigen Code, Inhalte sofort per Push an Clients zu übersetzen.

Gute Kandidaten für SignalR:

* Apps, die hoch Häufigkeits Updates vom Server erfordern. Beispiele hierfür sind Spiele, soziale Netzwerke, Abstimmungs-, Auktions-, Karten-und GPS-Apps.
* Dashboards und Überwachungs-apps. Beispiele hierfür sind Unternehmens Dashboards, sofortige Verkaufs Updates oder Reisewarnungen.
* Kollaborative apps. Whiteboardanwendungen und Team Besprechungs Software sind Beispiele für kollaborative apps.
* Apps, für die Benachrichtigungen erforderlich sind. Bei sozialen Netzwerken, e-Mails, Chats, spielen, Reisewarnungen und vielen anderen apps werden Benachrichtigungen verwendet.

SignalR stellt eine API zum Erstellen von [Remote Prozedur aufrufen (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)für Server-zu-Client bereit. Die RPCs wenden JavaScript-Funktionen auf Clients aus dem serverseitigen .net Core-Code an.

Im folgenden finden Sie einige Features der SignalR für ASP.net Core:

* Behandelt die Verbindungs Verwaltung automatisch.
* Sendet Nachrichten gleichzeitig an alle verbundenen Clients. Beispielsweise ein Chatraum.
* Sendet Nachrichten an bestimmte Clients oder Client Gruppen.
* Skaliert, um zunehmenden Datenverkehr zu verarbeiten.

Die Quelle wird in einem [SignalR Repository auf GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)gehostet.

## <a name="transports"></a>Transporte

SignalR unterstützt die folgenden Verfahren für die Verarbeitung der Echtzeitkommunikation (in der Reihenfolge des ordnungsgemäßen Fallbacks):

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Vom Server gesendete Ereignisse
* Langer Abruf

SignalR wählt automatisch die beste Transportmethode aus, die sich innerhalb der Funktionen des Servers und des Clients befindet.

## <a name="hubs"></a>Hubs

SignalR verwendet *Hubs* für die Kommunikation zwischen Clients und Servern.

Ein Hub ist eine Pipeline auf hoher Ebene, die es einem Client und einem Server ermöglicht, Methoden untereinander aufzurufen. SignalR behandelt die Verteilung über die Computer Grenzen hinweg automatisch, sodass Clients Methoden auf dem Server aufrufen können und umgekehrt. Sie können stark typisierte Parameter an Methoden übergeben, die die Modell Bindung ermöglichen. SignalR stellt zwei integrierte Hub-Protokolle bereit: ein auf JSON basierendes Text Protokoll und ein auf [messagepack](https://msgpack.org/)basierendes binäres Protokoll.  Im Allgemeinen erstellt messagepack kleinere Nachrichten im Vergleich zu JSON. Ältere Browser müssen [XHR Level 2](https://caniuse.com/#feat=xhr2) unterstützen, um Unterstützung für das messagepack-Protokoll bereitzustellen.

Hubs wenden Client seitigen Code an, indem Sie Nachrichten senden, die den Namen und die Parameter der Client seitigen Methode enthalten. Objekte, die als Methoden Parameter gesendet werden, werden mithilfe des konfigurierten Protokolls deserialisiert. Der Client versucht, den Namen einer Methode im Client seitigen Code zuzuordnen. Wenn der Client eine Entsprechung findet, ruft er die-Methode auf und übergibt die deserialisierten Parameterdaten an ihn.

## <a name="additional-resources"></a>Weitere Ressourcen

* [Beginnen Sie mit SignalR für ASP.net Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
