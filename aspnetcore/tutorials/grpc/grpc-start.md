---
title: 'Tutorial: Erste Schritte mit gRPC in ASP.NET Core'
author: juntaoluo
description: In dieser Tutorialreihe wird beschrieben, wie Sie einen gRPC-Dienst in ASP.NET Core erstellen. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672566"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>Tutorial: Erste Schritte mit dem gRPC-Dienst in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

In diesem Tutorial lernen Sie die Grundlagen der Erstellung eines gRPC-Diensts mit ASP.NET Core kennen.

Abschließend verfügen Sie über einen gRPC-Dienst, der Grußnachrichten ausgibt.

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen eines gRPC-Diensts
> * Ausführen des gRPC-Diensts.
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

### <a name="run-the-service"></a>Ausführen des Diensts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.

  Visual Studio führt den Dienst über die Eingabeaufforderung aus. In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Führen Sie das gRPC-Greeter-Projekt „GrpcGreeter“ über die Befehlszeile mit `dotnet run` aus. In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.

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
```

<!-- End of combined VS/Mac tabs -->

---

Im nächsten Tutorial wird gezeigt, wie ein gRPC-Client erstellt werden kann, was zum Testen des Greeter-Diensts erforderlich ist.

### <a name="examine-the-project-files-of-the-grpc-project"></a>Ansehen der gRPC-Projektdateien

gRPCGreeter-Dateien:

* greet.proto: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert. Weitere Informationen finden Sie unter <xref:grpc/index>.
* *Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.
* *appSettings.json*-Datei: Diese enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.
* *Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.
* Startup.cs: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

### <a name="test-the-service"></a>Testen des Diensts

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen eines gRPC-Diensts
> * Haben Sie den gRPC-Dienst ausgeführt.
> * Haben Sie die Projektdateien überprüft.

> [!div class="step-by-step"]
> [Weiter: Erstellen eines .NET Core-gRPC-Clients](xref:tutorials/grpc/grpc-client)