---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Erlernen Sie die grundlegenden Konzepte beim Schreiben von GrpC-Diensten mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925186"
---
# <a name="grpc-services-with-c"></a>GrpC-Dienste mit C\#

Dieses Dokument beschreibt die Konzepte, die zum Schreiben von [GrpC](https://grpc.io/docs/guides/) -apps in C#erforderlich sind. Die hier behandelten Themen gelten sowohl für [C-Core](https://grpc.io/blog/grpc-stacks)-basierte als auch für ASP.net Core-basierte GrpC-apps.

## <a name="proto-file"></a>Proto-Datei

gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung. Protokollpuffer (protobuf) werden standardmäßig als IDL (Interface Design Language) verwendet. Die Datei *@no__t -1. proto* enthält Folgendes:

* Die Definition des GrpC-Dienstanbieter.
* Die zwischen Clients und Servern gesendeten Nachrichten.

Weitere Informationen zur Syntax von protobuf-Dateien finden Sie in der [offiziellen Dokumentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Sehen Sie sich beispielsweise die Datei " *Gruß. proto* " an, die in den ersten Schritten [mit dem GrpC-Dienst](xref:tutorials/grpc/grpc-start)

* Definiert einen `Greeter` Dienst.
* Der `Greeter` Dienst definiert einen `SayHello` -Befehl.
* `SayHello`sendet eine `HelloRequest` Nachricht und empfängt eine `HelloReply` Meldung:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Hinzufügen einer. proto-Datei zu einer C\#-App

Die Datei *@no__t -1. proto* ist in einem Projekt enthalten, indem Sie der `<Protobuf>`-Element Gruppe hinzugefügt wird:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#-Toolunterstützung für PROTO-Dateien

Das Tool Paket " [GrpC. Tools](https://www.nuget.org/packages/Grpc.Tools/) " ist erforderlich, C# um die Assets aus *@no__t -3. proto* -Dateien zu generieren. Die generierten Assets (Dateien):

* Werden bei jeder Erstellung des Projekts Bedarfs abhängig generiert.
* Werden dem Projekt nicht hinzugefügt oder in die Quell Code Verwaltung eingecheckt.
* Ein buildartefakt, das im *obj* -Verzeichnis enthalten ist.

Dieses Paket ist für die Server-und Client Projekte erforderlich. Das `Grpc.AspNetCore` Metapaket enthält einen Verweis auf `Grpc.Tools`. Server Projekte können mithilfe `Grpc.AspNetCore` des Paket-Managers in Visual Studio oder durch Hinzufügen `<PackageReference>` einer zur Projektdatei hinzugefügt werden:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Client Projekte sollten neben den anderen Paketen, die für die Verwendung des GrpC-Clients erforderlich sind, direkt auf `Grpc.Tools` verweisen. Das Toolpaket ist zur Laufzeit nicht erforderlich, sodass die Abhängigkeit mit `PrivateAssets="All"`markiert ist:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Generierte C# Assets

Das Tool Paket generiert die C# Typen, die die Nachrichten darstellen, die in den enthaltenen *@no__t -2. proto* -Dateien definiert sind.

Bei serverseitigen Assets wird ein abstrakter Dienst Basistyp generiert. Der Basistyp enthält die Definitionen aller in der *. proto* -Datei enthaltenen GrpC-Aufrufe. Erstellen Sie eine konkrete Dienst Implementierung, die von diesem Basistyp abgeleitet ist und die Logik für die GrpC-Aufrufe implementiert. Für das `greet.proto`, das zuvor beschriebene Beispiel, wird ein `GreeterBase` abstrakter Typ generiert, `SayHello` der eine virtuelle Methode enthält. Eine konkrete Implementierung `GreeterService` überschreibt die-Methode und implementiert die Logik, die den GrpC-Befehl behandelt.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Bei Client seitigen Assets wird ein konkreter Clienttyp generiert. Die GrpC-Aufrufe in der *. proto* -Datei werden in Methoden für den konkreten Typ übersetzt, der aufgerufen werden kann. Für das `greet.proto`, das zuvor beschriebene Beispiel, wird ein `GreeterClient` konkreter Typ generiert. Ruft `GreeterClient.SayHelloAsync` auf, um einen GrpC-Rückruf an den Server zu initiieren.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Standardmäßig werden Server-und Client Objekte für jede @no__t Datei " *-1. proto* " generiert, die in der `<Protobuf>`-Element Gruppe enthalten ist. Um sicherzustellen, dass nur die Server Objekte in einem Server Projekt generiert `GrpcServices` werden, wird das `Server`-Attribut auf festgelegt.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Entsprechend ist das-Attribut in Client `Client` Projekten auf festgelegt.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
