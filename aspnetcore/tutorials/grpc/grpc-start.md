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
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="b4779-104">Tutorial: Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4779-104">Tutorial: Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="b4779-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b4779-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b4779-106">In diesem Tutorial lernen Sie die Grundlagen der Erstellung eines gRPC-Diensts mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="b4779-106">This tutorial teaches the basics of building a gRPC service on ASP.NET Core.</span></span>

<span data-ttu-id="b4779-107">Abschließend verfügen Sie über einen gRPC-Dienst, der Grußnachrichten ausgibt.</span><span class="sxs-lookup"><span data-stu-id="b4779-107">At the end, you'll have a gRPC service that echoes greetings.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

<span data-ttu-id="b4779-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b4779-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b4779-109">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="b4779-109">Create a gRPC service.</span></span>
> * <span data-ttu-id="b4779-110">Ausführen des gRPC-Diensts.</span><span class="sxs-lookup"><span data-stu-id="b4779-110">Run the gRPC service.</span></span>
> * <span data-ttu-id="b4779-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="b4779-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="b4779-112">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="b4779-112">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4779-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4779-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b4779-114">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b4779-114">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b4779-115">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="b4779-115">Create a new ASP.NET Core Web Application.</span></span>
  <span data-ttu-id="b4779-116">![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.1.png)</span><span class="sxs-lookup"><span data-stu-id="b4779-116">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.1.png)</span></span>
* <span data-ttu-id="b4779-117">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="b4779-117">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="b4779-118">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="b4779-118">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="b4779-119">![neue ASP.NET Core-Webanwendung](grpc-start/_static/np_3_0.2.png)</span><span class="sxs-lookup"><span data-stu-id="b4779-119">![new ASP.NET Core Web Application](grpc-start/_static/np_3_0.2.png)</span></span>
* <span data-ttu-id="b4779-120">Wählen Sie in der Dropdownliste **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-120">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown.</span></span> <span data-ttu-id="b4779-121">Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-121">Choose the **gRPC Service** template.</span></span>

  <span data-ttu-id="b4779-122">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="b4779-122">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](grpc-start/_static/se3.0.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4779-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4779-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b4779-125">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b4779-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b4779-126">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="b4779-126">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b4779-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b4779-127">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="b4779-128">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="b4779-128">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="b4779-129">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="b4779-129">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="b4779-130">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="b4779-130">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="b4779-131">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-131">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4779-132">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b4779-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b4779-133">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b4779-133">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="b4779-134">Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.</span><span class="sxs-lookup"><span data-stu-id="b4779-134">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="b4779-135">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="b4779-135">Open the project</span></span>

<span data-ttu-id="b4779-136">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie die Datei *GrpcGreeter.sln* aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-136">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="b4779-137">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="b4779-137">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4779-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4779-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b4779-139">Drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b4779-139">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="b4779-140">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-140">Visual Studio runs the service in a command prompt.</span></span> <span data-ttu-id="b4779-141">In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="b4779-141">The logs show that the service started listening on `http://localhost:50051`.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b4779-143">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b4779-143">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b4779-144">Führen Sie das gRPC-Greeter-Projekt „GrpcGreeter“ über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="b4779-144">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span> <span data-ttu-id="b4779-145">In den Protokollen wird vermerkt, dass der Dienst nun auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="b4779-145">The logs show that the service started listening on `http://localhost:50051`.</span></span>

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

<span data-ttu-id="b4779-146">Im nächsten Tutorial wird gezeigt, wie ein gRPC-Client erstellt werden kann, was zum Testen des Greeter-Diensts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b4779-146">The next tutorial will demonstrate how to build a gRPC client, which is required to test the Greeter service.</span></span>

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="b4779-147">Ansehen der gRPC-Projektdateien</span><span class="sxs-lookup"><span data-stu-id="b4779-147">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="b4779-148">gRPCGreeter-Dateien:</span><span class="sxs-lookup"><span data-stu-id="b4779-148">GrpcGreeter files:</span></span>

* <span data-ttu-id="b4779-149">greet.proto: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="b4779-149">greet.proto: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="b4779-150">Weitere Informationen finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="b4779-150">For more information, see <xref:grpc/index>.</span></span>
* <span data-ttu-id="b4779-151">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="b4779-151">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="b4779-152">*appSettings.json*-Datei: Diese enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="b4779-152">*appSettings.json*:Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="b4779-153">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b4779-153">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="b4779-154">*Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="b4779-154">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="b4779-155">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="b4779-155">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="b4779-156">Startup.cs: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="b4779-156">Startup.cs: Contains code that configures app behavior.</span></span> <span data-ttu-id="b4779-157">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b4779-157">For more information, see <xref:fundamentals/startup>.</span></span>

### <a name="test-the-service"></a><span data-ttu-id="b4779-158">Testen des Diensts</span><span class="sxs-lookup"><span data-stu-id="b4779-158">Test the service</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4779-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b4779-159">Additional resources</span></span>

<span data-ttu-id="b4779-160">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b4779-160">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b4779-161">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="b4779-161">Created a gRPC service.</span></span>
> * <span data-ttu-id="b4779-162">Haben Sie den gRPC-Dienst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4779-162">Ran the gRPC service.</span></span>
> * <span data-ttu-id="b4779-163">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="b4779-163">Examined the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b4779-164">Weiter: Erstellen eines .NET Core-gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="b4779-164">Next: Create a .NET Core gRPC client</span></span>](xref:tutorials/grpc/grpc-client)