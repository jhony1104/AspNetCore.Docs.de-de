---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Die grundlegenden Konzepte erfahren Sie, beim Schreiben von gRPC-Dienste mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 7c5ecf21124414b21f5c36b76e90bde67ac1f958
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672670"
---
# <a name="grpc-services-with-c"></a>gRPC-Dienste mit C\#

In diesem Dokument werden die Konzepte, die zum Schreiben benötigt [gRPC](https://grpc.io/docs/guides/) -apps in C#. Die hier behandelten Themen gelten für beide [C-Core](https://grpc.io/blog/grpc-stacks)-Basis und ASP.NET Core-basierte gRPC-apps.

## <a name="proto-file"></a>Proto-Datei

gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung. Als Schnittstelle Design Language (IDL) werden standardmäßig Protokollpuffer (Protobuf) verwendet werden. Die *.proto* -Datei enthält:

* Die Definition des gRPC-Diensts.
* Die Nachrichten zwischen Clients und Servern.

Weitere Informationen zur Syntax von Protobuf-Dateien finden Sie unter den [offizielle Dokumentation (Protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Betrachten Sie beispielsweise die *greet.proto* -Basisdatei [erste Schritte mit gRPC Service](xref:tutorials/grpc/grpc-start):

* Definiert eine `Greeter` Service.
* Die `Greeter` Dienst definiert eine `SayHello` aufrufen.
* `SayHello` sendet eine `HelloRequest` Nachricht und empfängt eine `HelloResponse` Nachricht:

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>Fügen Sie eine .proto-Datei für eine C-\# app

Die *.proto* Datei ist in einem Projekt enthalten, indem sie zum Hinzufügen der `<Protobuf>` Elementgruppe:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## <a name="c-tooling-support-for-proto-files"></a>C#Toolunterstützung für .proto-Dateien

Das Paket mit Tools [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) ist erforderlich, um das Generieren der C# Objekte aus den *.proto* Dateien. Der generierten Assets (Dateien):

* Werden auf einen Bedarf jedes Mal generiert, die das Projekt erstellt wird.
* Nicht dem Projekt hinzugefügt oder in die quellcodeverwaltung eingecheckt.
* Sind Sie ein buildartefakt, die innerhalb der *Obj* Verzeichnis.

Dieses Paket ist sowohl die Server- und Clientprojekte erforderlich. `Grpc.Tools` kann hinzugefügt werden, indem Sie mithilfe des Paket-Managers in Visual Studio oder das Hinzufügen einer `<PackageReference>` zu der Projektdatei:

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

Das Tools-Paket ist nicht erforderlich, während der Laufzeit, damit die Abhängigkeit mit markiert ist `PrivateAssets="All"`.

## <a name="generated-c-assets"></a>Generiert C# Assets

Das Tools-Paket generiert die C# Typen, die die Nachrichten, die in den enthaltenen definierten darstellen *.proto* Dateien.

Für serverseitige Ressourcen ist eine abstrakte Basisklasse Diensttyp generiert. Der Basistyp enthält die Definitionen aller gRPC Aufrufe innerhalb der *.proto* Datei. Erstellen Sie eine konkrete Implementierung, die von diesem Basistyp abgeleitet und implementiert die Logik für die gRPC-Aufrufe. Für die `greet.proto`, im Beispiel wird die zuvor beschriebenen einer abstraktes `GreeterBase` Typ, der eine virtuelle enthält `SayHello` Methode generiert. Eine konkrete Implementierung `GreeterService` überschreibt die Methode, und implementiert die Logik den gRPC-Aufruf zu verarbeiten.

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Für die clientseitige Objekte wird ein konkretes Clienttyp generiert. Die gRPC-Aufrufe in die *.proto* Datei werden in Methoden für den konkreten Typ, der aufgerufen werden kann, übersetzt. Für die `greet.proto`, zuvor beschriebenen einen konkreten Beispiel `GreeterClient` Typ generiert wird. Rufen Sie `GreeterClient.SayHello` eine gRPC-Aufrufs an den Server zu initiieren.

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

Standardmäßig werden Server und Client-Ressourcen für die einzelnen generiert *.proto* enthaltene Datei die `<Protobuf>` Elementgruppe. Um sicherzustellen, dass nur die Serverressourcen werden in einem Serverprojekt generiert die `GrpcServices` -Attributsatz auf `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

Auf ähnliche Weise wird das Attribut festgelegt, um `Client` in clientprojekten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
