---
title: 'Tutorial: Erste Schritte mit gRPC in ASP.NET Core'
author: juntaoluo
description: In dieser Tutorialreihe wird beschrieben, wie Sie einen gRPC-Dienst in ASP.NET Core erstellen. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 5f7a2f6b57804b3295b23c322dcbac553b05528b
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320055"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>Tutorial: Erste Schritte mit dem gRPC-Dienst in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

In diesem Tutorial lernen Sie die Grundlagen der Erstellung eines gRPC-Diensts mit ASP.NET Core kennen.

Abschließend verfügen Sie über einen gRPC-Dienst, der Grußnachrichten ausgibt.

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen eines gRPC-Diensts
> * Führen Sie den Dienst aus.
> * Überprüfen Sie die Projektdateien.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>Erstellen eines gRPC-Diensts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung.
  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.1.png)
* Nennen Sie das Projekt **GrpcGreeter**. Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.
  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.2.png)
* Wählen Sie in der Dropdownliste **.NET Core** und **ASP.NET Core 3.0** aus. Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.

  Das folgende Startprojekt wird erstellt:

  ![Projektmappen-Explorer](grpc-start/_static/se3.0.png)

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

### <a name="test-the-service"></a>Testen des Diensts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stellen Sie sicher, dass **GrpcGreeter.Server** als Startprojekt festgelegt ist, und drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.

  Visual Studio führt den Dienst über die Eingabeaufforderung aus. In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_start.png)

* Legen Sie nach dem Start der Dienstausführung **GrpcGreeter.Client** als Startprojekt fest, und drücken Sie STRG+F5, um den Client ohne den Debugger auszuführen.

  Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/client.png)

  Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Führen Sie das Serverprojekt „GrpcGreeter.Server“ über die Befehlszeile mit `dotnet run` aus. In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example\GrpcGreeter\GrpcGreeter.Server
```

* Führen Sie das Clientprojekt „GrpcGreeter.Client“ über die separate Befehlszeile mit `dotnet run` aus.

Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält. Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\tp\GrpcGreeter\GrpcGreeter.Server
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 107.46730000000001ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Ansehen der gRPC-Projektdateien

„GrpcGreeter.Server“-Dateien:

* greet.proto: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert. Weitere Informationen finden Sie unter <xref:grpc/index>.
* *Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.
* *appSettings.json*-Datei: Diese enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.
* *Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.
* Startup.cs

Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

gRPC-Clientdatei „GrpcGreeter.Client“:

*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen eines gRPC-Diensts
> * Ausführen eines Diensts und eines Clients zum Testen des Diensts
> * Haben Sie die Projektdateien überprüft.
