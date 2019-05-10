---
title: 'Tutorial: Erstellen eines .NET Core-Clients'
author: juntaoluo
description: In dieser Tutorialreihe wird beschrieben, wie Sie einen gRPC-Dienst in ASP.NET Core erstellen. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212639"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a>Tutorial: Erstellen eines .NET Core-Clients

Von [John Luo](https://github.com/juntaoluo)

In diesem Tutorial wird gezeigt, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client erstellen können, der mit gRPC-Diensten kommunizieren kann.

Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen Sie einen gRPC-Client.
> * Führen Sie den Dienst mit dem gRPC-Greeter-Dienst aus, der im vorherigen Tutorial erstellt wurde.
> * Überprüfen Sie die Projektdateien.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a>Erstellen einer .NET-Konsolenanwendung

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Folgen Sie [diesen](/dotnet/core/tutorials/with-visual-studio) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Folgen Sie [diesen](/dotnet/core/tutorials/with-visual-studio-code) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Folgen Sie [diesen](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a>Hinzufügen von erforderlichen Paketen

Fügen Sie die folgenden Pakete zum gRPC-Clientprojekt hinzu:

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core), das die C#-API für den C-Core-Client enthält.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält. Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.

Pakete können mit den folgenden Vorgehensweisen hinzugefügt werden:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a>PMC-Option zum Installieren von Paketen

* Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.
* Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.
* Führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package Grpc.Core
    ```

* Wiederholen Sie das `Install-Package` für Google.Protobuf und Grpc.Tools.

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a>Option „NuGet-Pakete verwalten“ zum Installieren von Paketen

* Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
* Legen Sie die **Paketquelle** auf „nuget.org“ fest.
* Geben Sie „Grpc.Core“ in das Suchfeld ein.
* Wählen Sie das Paket „Grpc.Core“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.
* Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.
* Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.
* Geben Sie „Grpc.Core“ in das Suchfeld ein.
* Wählen Sie das Paket „Grpc.Core“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.
* Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.

---

## <a name="add-the-greetproto-file"></a>Hinzufügen der„greet.proto“-Datei

Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt. Fügen Sie der `<Protobuf>`-Elementgruppe aus der GrpcGreeterClient-Projektdatei die Datei **greet.proto** hinzu:

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> Sie können die GrpcGreeterClient-Projektdatei durch einen Rechtsklick auf das Projekt und Auswählen der Option **Edit GrpcGreeterClient.csproj** (GrpcGreeterClient.csproj bearbeiten) aus dem Dropdownmenü öffnen.
>
> ![neue ASP.NET Core-Webanwendung](grpc-start/_static/edit_csproj.png)
>
> Alternativ können Sie zum GrpcGreeterClient-Verzeichnis navigieren und die Datei `GrpcGreeterClient.csproj` in Ihrem bevorzugten Editor bearbeiten.

Das `GrpcServices="Client"`-Attribut wird hinzugefügt, sodass nur die C#-Clientobjekte für die enthaltene Protobuf-Datei generiert werden. Kompilieren Sie das Clientprojekt, um die Generierung der C#-Clientobjekte auszulösen.

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a>Erstellen eines „GreeterClient“ und Aufrufen des unären Aufrufs „SayHello“

Fügen Sie der `Main`-Methode der `Program.cs`-Datei aus dem gRPC-Clientprojekt den folgenden Code hinzu:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

Für den Zugriff auf die erforderlichen Typen sind die folgenden using-Anweisungen erforderlich:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

Der GreeterClient wird erstellt, indem ein `Channel` instanziiert wird, der die Informationen zum Herstellen der Verbindung mit dem gRPC-Dienst enthält, und zum Erstellen des `GreeterClient` verwendet wird:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

Der GreeterClient enthält den unären Aufruf `SayHello`, der asynchron aufgerufen werden kann:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

Die Ergebnisse des Aufrufs `SayHello` werden in `reply` gespeichert, was dann angezeigt werden kann:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

Der vom Client verwendete `Channel` sollte deaktiviert werden, nachdem die Vorgänge zum Freigeben aller Ressourcen abgeschlossen sind:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> Bevor die Typen im **Greeter**-Namespace aufgelöst werden können, müssen Sie das Projekt erstellen. Diese Typen werden automatisch während des Buildvorgangs generiert und sind erst verfügbar, wenn ein Build ausgeführt wird.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stellen Sie sicher, dass der im vorherigen Tutorial erstellte Greeter-Dienst ausgeführt wird.

* Sobald der Dienst ausgeführt wird, kehren Sie zum **GrpcGreeterClient**-Projekt zurück und legen Sie dieses als Startprojekt fest. Drücken Sie STRG+F5, um die Ausführung des Clients ohne den Debugger zu starten.

  Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/client.png)

  Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Stellen Sie sicher, dass der im vorherigen Tutorial erstellte Greeter-Dienst ausgeführt wird.

* Führen Sie das Clientprojekt „GrpcGreeter.Client“ über die separate Befehlszeile mit `dotnet run` aus.

Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Ansehen der gRPC-Projektdateien

gRPC-Clientdatei „GrpcGreeterClient“:

*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen Sie einen gRPC-Client.
> * Führen Sie den Dienst mit dem gRPC-Greeter-Dienst aus, der im vorherigen Tutorial erstellt wurde.
> * Überprüfen Sie die Projektdateien.

> [!div class="step-by-step"]
> [Zurück: Erstellen eines gRPC-Greeter-Diensts](xref:tutorials/grpc/grpc-start)
