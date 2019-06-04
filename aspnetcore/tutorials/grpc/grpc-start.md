---
title: Erstellen eines .NET Core-gRPC-Clients und -Servers in ASP.NET Core
author: juntaoluo
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 5/30/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 2b4325d2413e335a3061a7695def88a1b23ee52b
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376370"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.

Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen Sie einen gRPC-Server.
> * Erstellen Sie einen gRPC-Client.
> * Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>Erstellen eines gRPC-Diensts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** aus.
* Klicken Sie auf **Weiter**.
* Nennen Sie das Projekt **GrpcGreeter**. Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.
* Klicken Sie auf **Erstellen**.
* Führen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** folgende Aktionen durch:
  * Wählen Sie in den Dropdownmenüs **.NET Core** und **ASP.NET Core 3.0** aus. 
  * Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.
  * Klicken Sie auf **Erstellen**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie die folgenden Befehle aus:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.
  * Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.

  Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**
* Wählen Sie **Ja** aus.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie über ein Terminal die folgenden Befehle aus:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.

### <a name="open-the-project"></a>Öffnen des Projekts

Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie die Datei *GrpcGreeter.sln* aus.

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a>Ausführen des Diensts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.

  Visual Studio führt den Dienst über die Eingabeaufforderung aus.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Führen Sie das gRPC-Greeter-Projekt „GrpcGreeter“ über die Befehlszeile mit `dotnet run` aus.

<!-- End of combined VS/Mac tabs -->

---

In den Protokollen wird vermerkt, dass der Dienst auf `http://localhost:50051` lauscht.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

gRPCGreeter-Dateien:

* *greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert. Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).
* *Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.
* *appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.
* *Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.
* *Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Erstellen des gRPC-Clients in einer .NET-Konsolen-App

## <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Datei** > **Neu** > **Projekt** aus der Menüleiste aus.
* Wählen Sie im Dialogfeld **Neues Projekt** **Konsolen-App (.NET Core)** aus.
* Klicken Sie auf **Weiter**.
* Geben sie im Textfeld **Name** „GrpcGreeterClient“ ein.
* Wählen Sie **Erstellen** aus.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie die folgenden Befehle aus:

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Folgen Sie [diesen](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a>Hinzufügen von erforderlichen Paketen

Fügen Sie die folgenden Pakete zum gRPC-Clientprojekt hinzu:

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core), das die C#-API für den C-Core-Client enthält.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält. Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.

####  <a name="pmc-option-to-install-packages"></a>PMC-Option zum Installieren von Paketen

* Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.
* Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.
* Führen Sie die folgenden Befehle aus:

 ```powershell
Install-Package Grpc.Core
Install-Package Grpc.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Option „NuGet-Pakete verwalten“ zum Installieren von Paketen

* Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
* Wählen Sie die Registerkarte **Durchsuchen** aus.
* Geben Sie **Grpc.Core** in das Suchfeld ein.
* Wählen Sie das Paket **Grpc.Core** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.
* Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner **Pakete** unter **Lösungspad** > **Pakete hinzufügen**.
* Geben Sie **Grpc.Core** in das Suchfeld ein.
* Wählen Sie das Paket **Grpc.Core** aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.
* Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.

---

### <a name="add-greetproto"></a>Fügen Sie „greet.proto“ hinzu:

* Erstellen Sie einen **Protos**-Ordner im gRPC-Clientprojekt.
* Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.
* Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

  Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **GrpcGreeterClient.csproj bearbeiten** aus.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

  Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Extras > Datei bearbeiten** aus.

  ------

* Fügen Sie der `<Protobuf>`-Elementgruppe aus der GrpcGreeterClient-Projektdatei die Datei **greet.proto** hinzu:

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

Kompilieren Sie das Clientprojekt, um die Generierung der C#-Clientobjekte auszulösen.

### <a name="create-the-greater-client"></a>Erstellen des Greeterclients

Erstellen Sie das Projekt, um die Typen im **Greeter**-Namespace erstellen zu können. Die `Greeter`-Typen werden vom Buildprozess automatisch erstellt.

Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.

Der Greeterclient wird folgendermaßen erstellt:

* Instanziieren eines `Channel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält
* Verwenden des `Channel`-Objekts zum Erstellen des Greeterclients:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-6)]

Der Greeterclient ruft die asynchrone Methode `SayHello` auf. Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

Halten Sie das vom Client verwendete `Channel`-Objekt an, nachdem die Vorgänge zum Freigeben aller Ressourcen abgeschlossen sind:

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie im Greeterdienst STRG+F5, um den Server ohne Debugger zu starten.
* Drücken Sie im GrpcGreeterClient-Projekt STRG+F5, um den Server ohne Debugger zu starten.

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Starten Sie den Greeterdienst.
* Starten Sie den Client.

<!-- End of combined VS/Mac tabs -->

---

Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus. Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Der gRPC-Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a>Nächste Schritte

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
