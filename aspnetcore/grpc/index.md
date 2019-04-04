---
title: Einführung in gRPC in ASP.NET Core
author: juntaoluo
description: Informationen zu gRPC-Diensten mit dem Kestrel-Server und dem ASP.NET Core-Stapel
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142415"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a>Einführung in gRPC in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf). Weitere grundlegende Informationen zu gRPC finden Sie in der [gRPC-Dokumentation](https://grpc.io/docs/).

Im Folgenden sind die Hauptvorteile von gRPC aufgelistet:
* Ein modernes, hochleistungsfähiges und reduziertes Framework für Remoteprozeduraufrufe
* Contract First-API-Entwicklung mit standardmäßiger Verwendung von Protokollpuffern, wodurch sprachunabhängige Implementierungen möglich sind
* Für viele Sprachen verfügbare Tools zur Generierung stark typisierter Server und Clients
* Unterstützung von Clients, Servern und bidirektionalen Streamingaufrufen
* Reduzierte Netzwerkauslastung mit binärer Protobuf-Serialisierung

gRPC ist für die folgenden Komponenten besonders geeignet:
* Einfache Microservices, bei denen Effizienz essentiell ist
* Mehrsprachige Systeme, bei denen mehrere Sprachen für die Entwicklung erforderlich sind
* Point-to-Point-Dienste, die in Echtzeit Streaminganforderungen oder -antworten verarbeiten müssen

Eine C#-Implementierung ist derzeit auf der offiziellen [gRPC-Seite](https://grpc.io/docs/quickstart/csharp.html) verfügbar. Die aktuelle Implementierung basiert auf der nativen Bibliothek, die in C geschrieben ist (gRPC [C-core](https://grpc.io/blog/grpc-stacks) (Visualisierung von gRPC-Sprachstapeln)). Wir arbeiten derzeit an der Bereitstellung einer neuen Implementierung, die vollständig verwaltet ist und die auf dem Kestrel-HTTP-Server sowie auf dem ASP.NET Core-Stapel basiert. In den folgenden Dokumenten finden Sie eine Einführung in die Erstellung von gRPC-Diensten mit dieser neuen Implementierung.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>