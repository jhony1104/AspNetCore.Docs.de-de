---
title: Hostingmodelle Razor-Komponenten
author: guardrex
description: Verstehen der clientseitigen Blazor und ASP.NET Core Razor Serverkomponenten Hostingmodelle.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515526"
---
# <a name="razor-components-hosting-models"></a>Hostingmodelle Razor-Komponenten

Durch [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten ist ein Webframework, das für die clientseitige Ausführung vorgesehen im Browser auf eine [WebAssembly](http://webassembly.org/)-basierte .NET Common Language Runtime (*Blazor*) oder serverseitige in ASP.NET Core (*ASP.NET Core Razor Komponenten*). Unabhängig von dem Modell, die app und die Komponente Hostingmodelle *unverändert*. In diesem Artikel werden die verfügbaren Hostingmodelle erläutert:

* [Clientseitiges Blazor](#client-side-hosting-model)
* [Serverseitige .NET Core-Razor-Komponenten](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a>Clientseitiges Hostingmodell

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Das Dienstprinzipale Hostingmodell für Blazor ist im Browser auf WebAssembly clientseitige ausgeführt. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. Aktualisierungen der Benutzeroberfläche und Behandlung von Ereignissen treten innerhalb des gleichen Prozesses. Die app Ressourcen werden als statische Dateien an einen Webserver oder ein Dienst kann das Verarbeiten von statischen Inhalten an Clients bereitgestellt.

![Blazor clientseitig: Die Blazor-Anwendung, die in einem UI-Thread im Browser ausgeführt werden.](hosting-models/_static/client-side.png)

Um eine Blazor-app, die über das Client-Side-hosting-Modell erstellen, verwenden Sie eine der folgenden Vorlagen:

* **Blazor** ([Dotnet neue Blazor](/dotnet/core/tools/dotnet-new)) &ndash; als einen Satz von statischen Dateien bereitgestellt.
* **(ASP.NET Core gehostet) Blazor** ([Dotnet neue Blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; von einem ASP.NET Core-Server gehostet wird. Die ASP.NET Core-app dient die app Blazor Clients. Die clientseitige Blazor-app kann über das Netzwerk mithilfe von Web-API-Aufrufen mit dem Server interagieren oder [SignalR](xref:signalr/introduction).

Die Vorlagen enthalten die *components.webassembly.js* -Skript, das behandelt:

* Herunterladen von .NET Runtime, die app und die app Abhängigkeiten.
* Die Initialisierung der Runtime, um die app ausführen.

Das Hostingmodell für die clientseitige bietet mehrere Vorteile. Die clientseitige Blazor:

* Verfügt über keine serverseitige-Abhängigkeit von .NET.
* Vollständig nutzt die Clientressourcen und Fähigkeiten.
* Abladungen Arbeit vom Server an den Client.
* Unterstützt die offline-Szenarien.

Einige Nachteile der clientseitigen hosten. Die clientseitige Blazor:

* Wird die app auf die Funktionen des Browsers beschränkt.
* Erfordert, kann der Clienthardware und Software (z. B. WebAssembly unterstützt).
* Verfügt über eine größere Downloadgröße und länger Ladevorgang der app.
* Hat weniger Reife der .NET Common Language Runtime und die Unterstützung von Tools (z. B. Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen).

## <a name="server-side-hosting-model"></a>Serverseitiges Hostingmodell

Mit dem ASP.NET Core-Razor-Komponenten serverseitige hosting-Modell wird die app auf dem Server in einer ASP.NET Core-app ausgeführt. Aktualisierungen der Benutzeroberfläche, Ereignisbehandlung und JavaScript-Aufrufe erfolgt über eine [SignalR](xref:signalr/introduction) Verbindung.

![ASP.NET Core-Razor-Komponenten Serverseitig: Der Browser interagiert mit der app, die (gehostet in einer ASP.NET Core-app) auf dem Server über eine SignalR-Verbindung.](hosting-models/_static/server-side.png)

Verwenden Sie zum Erstellen einer Razor-Komponenten-app, die mit das Hostingmodell für die serverseitige die ASP.NET Core **Razor-Komponenten** Vorlage ([Dotnet neue Razorcomponents](/dotnet/core/tools/dotnet-new)). Die ASP.NET Core-app hostet die serverseitige app Razor-Komponenten und richtet den SignalR-Endpunkt, in denen Clients verbinden. Die ASP.NET Core-app verweist auf der app `Startup` Klasse hinzufügen:

* Serverseitige Komponenten der Razor-Dienste.
* Die app die Anforderungspipeline.

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

Die *components.server.js* Skript&dagger; stellt die Clientverbindung her. Es ist der Zuständigkeit der app zum beibehalten und Wiederherstellen von app-Status nach Bedarf (z. B. im Falle einer Netzwerkverbindung verloren).

Das Hostingmodell für die serverseitige bietet mehrere Vorteile. Serverseitige Razor-Komponenten:

* Verfügen über eine erheblich kleinere app-Größe als eine clientseitige Blazor-app und geladen Sie sehr viel schneller.
* Können vollständig von Serverfunktionen, einschließlich der Verwendung von .NET Core kompatible APIs nutzen.
* In .NET Core auf dem Server ausgeführt, damit vorhandene .NET Tools, z. B. Debuggen, wie erwartet funktioniert.
* Funktioniert mit thin Clients (z. B. Browser, WebAssembly und die Ressourcengruppe nicht unterstützen, Geräten eingeschränkt).
* .NET /C# Codebasis, einschließlich der Code der app Komponente ist nicht für Clients bereitgestellt.

Einige Nachteile: serverseitige hosten. Serverseitige Razor-Komponenten:

* Haben Sie die höhere Latenz: Jedes Eingreifen des Benutzers umfasst ein Netzwerkhop.
* Bieten Sie keine offline-Unterstützung: Wenn die Clientverbindung fehlschlägt, funktioniert die app nicht mehr.
* Eingeschränkte Skalierbarkeit: Der Server muss mehrere Clientverbindungen verwalten und Clientstatus behandeln.
* ASP.NET Core Server zum Bereitstellen der app erforderlich ist. Bereitstellung ohne Server (z. B. von einem CDN) ist nicht möglich.

&dagger;Die *components.server.js* Skript wird in folgendem Pfad veröffentlicht: *"bin" / {Debuggen | Das Release} / {ZIELFRAMEWORK} /publish/ {ANWENDUNGSNAME}. App/Dist/_framework*.
