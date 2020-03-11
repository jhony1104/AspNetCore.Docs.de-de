---
page_type: sample
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648157"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Erstellen eines gRPC-Clients und -Servers in ASP.NET Core 3.0 mit Visual Studio

In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.

Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.

In diesem Tutorial:

* Erstellen Sie einen gRPC-Server.
* Erstellen Sie einen gRPC-Client.
* Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).

## <a name="create-a-grpc-service"></a>Erstellen eines gRPC-Diensts

* Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.
* Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** aus.
* Klicken Sie auf **Weiter**.
* Nennen Sie das Projekt **GrpcGreeter**. Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.
* Klicken Sie auf **Erstellen**.
* Führen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** folgende Aktionen durch:
  * Wählen Sie in den Dropdownmenüs **.NET Core** und **ASP.NET Core 3.0** aus. 
  * Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.
  * Klicken Sie auf **Erstellen**.

### <a name="run-the-service"></a>Ausführen des Diensts

* Drücken Sie `Ctrl+F5`, um den gRPC-Dienst ohne den Debugger auszuführen.

  Visual Studio führt den Dienst über die Eingabeaufforderung aus.

In den Protokollen wird vermerkt, dass der Dienst auf `https://localhost:5001` lauscht.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> Die gRPC-Vorlage ist für die Verwendung von [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) konfiguriert. gRPC-Clients müssen zum Aufrufen des Servers HTTPS verwenden.
>
> macOS unterstützt ASP.NET Core gRPC mit TLS nicht. Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [gRPC und ASP.NET Core unter macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

*GrpcGreeter*-Projektdateien:

* *greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert. Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).
* *Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.
* *appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.
* *Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Erstellen des gRPC-Clients in einer .NET-Konsolen-App

* Öffnen Sie eine zweite Instanz von Visual Studio.
* Wählen Sie **Datei** > **Neu** > **Projekt** aus der Menüleiste aus.
* Wählen Sie im Dialogfeld **Neues Projekt** **Konsolen-App (.NET Core)** aus.
* Klicken Sie auf **Weiter**.
* Geben sie im Textfeld **Name** „GrpcGreeterClient“ ein.
* Wählen Sie **Erstellen** aus.

### <a name="add-required-packages"></a>Hinzufügen von erforderlichen Paketen

Das gRPC-Clientprojekt erfordert die folgenden Pakete:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält. Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.

Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.

#### <a name="pmc-option-to-install-packages"></a>PMC-Option zum Installieren von Paketen

* Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.
* Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.
* Führen Sie die folgenden Befehle aus:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Option „NuGet-Pakete verwalten“ zum Installieren von Paketen

* Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
* Wählen Sie die Registerkarte **Durchsuchen** aus.
* Geben Sie **Grpc.Net.Client** in das Suchfeld ein.
* Wählen Sie das Paket **Grpc.Net.Client** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.
* Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.

### <a name="add-greetproto"></a>Fügen Sie „greet.proto“ hinzu:

* Erstellen Sie einen **Protos**-Ordner im gRPC-Clientprojekt.
* Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.
* Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:

  Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.

* Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei **greet.proto** verweist:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Erstellen des Greeter-Clients

Erstellen Sie das Projekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen. Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.

Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.

Der Greeter-Client wird folgendermaßen erstellt:

* Instanziieren eines `GrpcChannel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält
* Verwenden von `GrpcChannel` zum Erstellen des Greeter-Clients.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst

* Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.
* Drücken Sie im `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Client ohne den Debugger zu starten.

Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus. Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Der gRPC-Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>Hilfe aus der Dokumentation und nächste Schritte für gRPC

* [Einführung in gRPC in ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [gRPC-Dienste mit C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrieren von gRPC-Diensten von C-core zu ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
