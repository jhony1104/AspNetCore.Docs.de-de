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
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="8b941-104">Tutorial: Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b941-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="8b941-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="8b941-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="8b941-106">In diesem Tutorial lernen Sie die Grundlagen der Erstellung eines gRPC-Diensts mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="8b941-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="8b941-107">Abschließend verfügen Sie über einen gRPC-Dienst, der Grußnachrichten ausgibt.</span><span class="sxs-lookup"><span data-stu-id="8b941-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="8b941-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="8b941-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b941-109">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="8b941-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="8b941-110">Führen Sie den Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-110">Run the service.</span></span>
> * <span data-ttu-id="8b941-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="8b941-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="8b941-112">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="8b941-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b941-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b941-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b941-114">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8b941-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8b941-115">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="8b941-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="8b941-116">![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="8b941-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="8b941-117">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="8b941-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="8b941-118">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="8b941-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="8b941-119">![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="8b941-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="8b941-120">Wählen Sie in der Dropdownliste **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="8b941-121">Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="8b941-122">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="8b941-122">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b941-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b941-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8b941-125">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8b941-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8b941-126">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="8b941-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8b941-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8b941-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="8b941-128">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="8b941-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="8b941-129">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="8b941-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="8b941-130">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="8b941-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="8b941-131">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b941-132">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8b941-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b941-133">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8b941-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="8b941-134">Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.</span><span class="sxs-lookup"><span data-stu-id="8b941-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="8b941-135">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="8b941-135">Open the project</span></span>

<span data-ttu-id="8b941-136">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie die Datei *GrpcGreeter.sln* aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="test-the-service"></a><span data-ttu-id="8b941-137">Testen des Diensts</span><span class="sxs-lookup"><span data-stu-id="8b941-137">Test the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b941-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b941-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b941-139">Stellen Sie sicher, dass **GrpcGreeter.Server** als Startprojekt festgelegt ist, und drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8b941-139">Ensure the **GrpcGreeter.Server** is set as the Startup Project and press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="8b941-140">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="8b941-141">In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="8b941-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_start.png)

* <span data-ttu-id="8b941-143">Legen Sie nach dem Start der Dienstausführung **GrpcGreeter.Client** als Startprojekt fest, und drücken Sie STRG+F5, um den Client ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8b941-143">Once the service is running, set the **GrpcGreeter.Client** is set as the Startup Project and press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="8b941-144">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="8b941-144">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="8b941-145">Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="8b941-145">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/client.png)

  <span data-ttu-id="8b941-147">Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8b941-147">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="8b941-149">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8b941-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8b941-150">Führen Sie das Serverprojekt „GrpcGreeter.Server“ über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-150">Run the Server project GrpcGreeter.Server from the command line using `dotnet run`.</span></span> <span data-ttu-id="8b941-151">In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="8b941-151">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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

* <span data-ttu-id="8b941-152">Führen Sie das Clientprojekt „GrpcGreeter.Client“ über die separate Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="8b941-152">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="8b941-153">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="8b941-153">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="8b941-154">Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="8b941-154">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="8b941-155">Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8b941-155">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="8b941-156">Ansehen der gRPC-Projektdateien</span><span class="sxs-lookup"><span data-stu-id="8b941-156">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="8b941-157">„GrpcGreeter.Server“-Dateien:</span><span class="sxs-lookup"><span data-stu-id="8b941-157">GrpcGreeter.Server files:</span></span>

* <span data-ttu-id="8b941-158">greet.proto: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="8b941-158">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="8b941-159">Weitere Informationen finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8b941-159">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="8b941-160">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="8b941-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="8b941-161">*appSettings.json*-Datei: Diese enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="8b941-161">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="8b941-162">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8b941-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="8b941-163">*Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="8b941-163">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="8b941-164">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="8b941-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="8b941-165">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8b941-165">Startup.cs</span></span>

<span data-ttu-id="8b941-166">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="8b941-166">Contains code that configures app behavior.</span></span> <span data-ttu-id="8b941-167">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8b941-167">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="8b941-168">gRPC-Clientdatei „GrpcGreeter.Client“:</span><span class="sxs-lookup"><span data-stu-id="8b941-168">gRPC client GrpcGreeter.Client file:</span></span>

<span data-ttu-id="8b941-169">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="8b941-169">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="8b941-170">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="8b941-170">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8b941-171">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="8b941-171">Created a gRPC service.</span></span>
> * <span data-ttu-id="8b941-172">Ausführen eines Diensts und eines Clients zum Testen des Diensts</span><span class="sxs-lookup"><span data-stu-id="8b941-172">Ran the service and a client to test the service.</span></span>
> * <span data-ttu-id="8b941-173">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="8b941-173">Examined the project files.</span></span>
