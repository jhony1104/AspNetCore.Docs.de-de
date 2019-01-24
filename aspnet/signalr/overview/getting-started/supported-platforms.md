---
uid: signalr/overview/getting-started/supported-platforms
title: Unterstützte Plattformen | Microsoft-Dokumentation
author: bradygaster
description: In diesem Artikel wird beschrieben, welche Clients und Servern von SignalR unterstützt werden.
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 60fa74b54797efbe14ba525160b2f750a4f5a451
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836050"
---
<a name="supported-platforms"></a>Unterstützte Plattformen
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Artikel wird beschrieben, welche Clients und Servern von SignalR unterstützt werden. 
> 
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
> 
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).

SignalR ist unter einer Vielzahl von Server- und Client-Konfigurationen unterstützt. Darüber hinaus verfügt über jeden Transportoption auf einen Satz von Anforderungen über eigene aus. Wenn Sie die Systemanforderungen für einen Transport nicht verfügbar sind, wird SignalR ordnungsgemäß Failover auf andere Transporte. Weitere Informationen zu die Transporte, die von SignalR unterstützt, finden Sie unter [Transporte und Fallbacks](introduction-to-signalr.md#transports).

## <a name="server-system-requirements"></a>Server – Systemanforderungen

Die SignalR-Serverkomponenten kann auf einer Vielzahl von Serverkonfigurationen gehostet werden. Dieser Abschnitt beschreibt die unterstützten Versionen von Betriebssystemen, .NET Framework, IIS-Server und andere Komponenten.

### <a name="supported-server-operating-systems"></a>Unterstützte Serverbetriebssysteme

Die SignalR-Serverkomponenten kann in den folgenden Server oder Client-Betriebssystemen gehostet werden. Beachten Sie, dass für die SignalR verwendet WebSockets, Windows Server 2012, Windows Server 2016 oder Windows 8 erforderlich ist (WebSocket kann verwendet werden auf Windows Azure-Websites, .NET Framework-Version des Standorts auf 4.5 festgelegt ist, und die Web Sockets in der Website aktiviert ist Configuration-Seite).

- Windows Server 2016
- Windows Server 2012
- Windows Server 2008 r2
- Windows 10
- Windows 8
- Windows 7
- Windows Azure

### <a name="supported-server-net-framework-version"></a>.NET Framework-Version unterstützte server

SignalR 2 wird nur unter .NET Framework 4.5 unterstützt. Finden Sie unter den [empfohlene Updates](#updates) Abschnitt für Updates, die Zuverlässigkeit, Kompatibilität, Stabilität und Leistung zu verbessern.

### <a name="supported-server-iis-versions"></a>Unterstützter Server IIS-Versionen

Wenn Sie SignalR in IIS gehostet wird, werden die folgenden Versionen unterstützt. Beachten Sie, dass wenn ein Client-Betriebssystem verwendet wird, wie z. B. für die Entwicklung (Windows 8 oder Windows 7), einen vollständige Versionen von IIS oder Cassini nicht verwendet werden sollte, da es werden maximal 10 gleichzeitige Verbindungen auferlegt, die sehr schnell da Verbindungen erreicht wird, vorübergehende, häufig erneut hergestellt, und Sie werden nicht sofort entfernt bei nicht mehr verwendet wird. IIS Express sollte auf Client-Betriebssystemen verwendet werden.

Beachten Sie außerdem, dass für SignalR WebSocket, verwendet IIS 8 oder IIS 8 Express verwendet werden muss, den der Server Windows 8, WindowsServer 2012 oder höher verwendet werden muss, und WebSocket in IIS aktiviert sein muss. Informationen zum Aktivieren von WebSocket in IIS finden Sie unter [Unterstützung für IIS 8.0 WebSocket-Protokolls](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

- IIS 8 oder IIS 8 Express.
- IIS 7 und 7.5. Unterstützung für [URLs ohne Erweiterung](https://support.microsoft.com/kb/980368) ist erforderlich.
- IIS muss im integrierten Modus ausgeführt werden; im klassischen Modus wird nicht unterstützt. Nachrichtenverzögerungen von bis zu 30 Sekunden möglicherweise auftreten können, wenn im klassischen Modus mithilfe des Server-Sent Ereignisse Transports IIS ausgeführt wird.
- Die hostanwendung muss im Modus mit vollständiger Vertrauenswürdigkeit ausgeführt werden.

## <a name="client-system-requirements"></a>Systemanforderungen für Clients

SignalR kann in einer Vielzahl von Client-Plattformen verwendet werden. Dieser Abschnitt beschreibt die Systemanforderungen für die Verwendung von SignalR im Webbrowser, Windows-desktopanwendungen, Silverlight-Anwendungen und mobilen Geräten.

### <a name="web-browsers"></a>Webbrowser

SignalR kann in einer Vielzahl von Webbrowsern verwendet werden, aber in der Regel nur die letzten beiden Versionen werden unterstützt.

Anwendungen, die verwenden SignalR in Browsern, müssen jQuery-Version 1.6.4 oder höher Hauptversionen (z. B. 1.7.2 1.8.2 oder 1.9.1) verwenden.

SignalR kann in den folgenden Browsern verwendet werden:

- Microsoft Internet Explorer, Versionen 8, 9, 10 und 11. Moderne, Desktop- und Mobile-Versionen verwendet werden.
- Mozilla Firefox: aktuelle Version - 1, sowohl Windows und Mac-Versionen.
- Google Chrome: aktuelle Version – 1, sowohl Windows und Mac-Versionen.
- Safari: aktuelle Version - 1, Mac und iOS-Versionen.
- Opera: aktuelle Version – 1, nur Windows.
- Android-browser

Zusätzlich zum Einbinden von bestimmter Browser, gelten die verschiedene Transporte, die SignalR verwendet Ihren eigenen. Die folgenden Transportoptionen werden unter den folgenden Konfigurationen unterstützt:

<a id="browser"></a>

**Transport Anforderungen an Webbrowser**

| Transport | Internet Explorer | Chrome (Windows oder iOS) | Firefox | Safari (OS x oder iOS) | Android |
| --- | --- | --- | --- | --- | --- |
| WebSockets | 10+ | aktuelle - 1 | aktuelle - 1 | aktuelle - 1 | Nicht zutreffend |
| Vom Server gesendeten Ereignisse | Nicht zutreffend | aktuelle - 1 | aktuelle - 1 | aktuelle - 1 | Nicht zutreffend |
| ForeverFrame | 8+ | Nicht zutreffend | Nicht zutreffend | Nicht zutreffend | 4.1 |
| Lange Abrufvorgänge | 8+ | aktuelle - 1 | aktuelle - 1 | aktuelle - 1 | 4.1 |

\*: 6 + für die vollständige Funktionalität erforderlich.

#### <a name="unsupported-browsers"></a>Nicht unterstützte Browser

Während Sie SignalR *möglicherweise* ausführen, ohne wichtige Probleme in ältere Browserversionen, wir Testen nicht aktiv SignalR darin und beheben Sie in der Regel keine Fehler, die in der sie vorkommen.

### <a name="windows-desktop-and-silverlight-applications"></a>Windows-Desktop und Silverlight-Anwendungen

Zusätzlich zur Ausführung in einem Webbrowser, kann die SignalR in eigenständigen Windows-Client oder Silverlight-Anwendungen gehostet werden. Windows-Desktop und Silverlight-SignalR-Anwendungen müssen die folgenden Systemanforderungen.

- Anwendungen, die mit .NET 4 werden unter Windows XP SP3 oder höher unterstützt.
- Anwendungen mit .NET Framework 4.5 werden unter Windows Vista oder höher unterstützt.

Zusätzlich zum Betriebssystem und .NET Framework-Anforderungen gelten die Transporte, die SignalR zur eigenen. Die folgenden Transportoptionen werden unter den folgenden Konfigurationen unterstützt:

**Windows-Desktop und Silverlight-Transport-Anforderungen**

| Transport | Anwendung für .NET | Silverlight |
| --- | --- | --- |
| WebSockets | Windows 8 und höher und .NET 4.5 und höher | Nicht zutreffend |
| Forever Frame | Nicht zutreffend | Nicht zutreffend |
| Vom Server gesendeten Ereignisse | .NET 4+ | 5+ |
| Lange Abrufvorgänge | .NET 4+ | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows Store und Windows Phone-Anwendungen

SignalR kann in Windows Store-Anwendungen und Windows Phone 8-Anwendungen verwendet werden. Die folgenden Transportoptionen werden unter den folgenden Konfigurationen unterstützt:

**Windows Store und Windows Phone-Transport-Anforderungen**

| Transport | Windows Store/ .NET | Windows Store / JavaScript | Windows Phone/ IE | Windows Phone/ .NET |
| --- | --- | --- | --- | --- |
| WebSockets | Nicht zutreffend | Win8+ | 8+ | Nicht zutreffend |
| Forever Frame | Nicht zutreffend | Win8+ | 7.5+ | Nicht zutreffend |
| Vom Server gesendeten Ereignisse | Win8+ | Nicht zutreffend | Nicht zutreffend | 8+ |
| Lange Abrufvorgänge | Win8+ | Win8+ | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>Empfohlene Updates

Die folgenden Updates werden für SignalR-Server empfohlen:

- Ist ein Update für .NET Framework 4.5 verfügbar [hier](https://support.microsoft.com/kb/2750149).
- Microsoft wird in regelmäßigen Abständen QFEs für ASP.NET freigeben. Diese sollten je nach Verfügbarkeit angewendet werden.
