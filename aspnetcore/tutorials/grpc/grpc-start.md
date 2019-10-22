---
title: Erstellen eines .NET Core-gRPC-Clients und -Servers in ASP.NET Core
author: juntaoluo
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/10/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 61324cdd5b574ea8a12a1be5846a25c311ab4499
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259668"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="97dd9-104">Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97dd9-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="97dd9-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="97dd9-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="97dd9-106">In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.</span><span class="sxs-lookup"><span data-stu-id="97dd9-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="97dd9-107">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="97dd9-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="97dd9-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="97dd9-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="97dd9-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="97dd9-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97dd9-110">Erstellen Sie einen gRPC-Server.</span><span class="sxs-lookup"><span data-stu-id="97dd9-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="97dd9-111">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="97dd9-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="97dd9-112">Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).</span><span class="sxs-lookup"><span data-stu-id="97dd9-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="97dd9-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="97dd9-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="97dd9-117">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="97dd9-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97dd9-119">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="97dd9-120">Alternativ dazu können Sie auch im Visual Studio-Menü **Datei** auf **Neu** > **Projekt** klicken.</span><span class="sxs-lookup"><span data-stu-id="97dd9-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="97dd9-121">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **gRPC-Dienst** aus, und klicken Sie auf **Weiter**:</span><span class="sxs-lookup"><span data-stu-id="97dd9-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Dialogfeld **Neues Projekt erstellen**](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="97dd9-123">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="97dd9-124">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="97dd9-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="97dd9-125">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-125">Select **Create**.</span></span>
* <span data-ttu-id="97dd9-126">Im Dialogfeld **Neuen gRPC-Dienst erstellen**:</span><span class="sxs-lookup"><span data-stu-id="97dd9-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="97dd9-127">Die **gRPC-Dienst** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="97dd9-128">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-128">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97dd9-130">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="97dd9-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="97dd9-131">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="97dd9-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="97dd9-132">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="97dd9-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="97dd9-133">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="97dd9-134">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="97dd9-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="97dd9-135">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="97dd9-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="97dd9-136">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="97dd9-138">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="97dd9-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="97dd9-139">Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="97dd9-140">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="97dd9-140">Open the project</span></span>

<span data-ttu-id="97dd9-141">Klicken Sie in Visual Studio auf **Datei** > **Öffnen**, und wählen Sie die Datei *GrpcGreeter.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="97dd9-142">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="97dd9-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97dd9-144">Drücken Sie `Ctrl+F5`, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="97dd9-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="97dd9-145">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-146">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97dd9-147">Führen Sie das gRPC-Greeter-Projekt *GrpcGreeter* über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="97dd9-149">Führen Sie das gRPC-Greeter-Projekt *GrpcGreeter* über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-149">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

---

<span data-ttu-id="97dd9-150">In den Protokollen wird vermerkt, dass der Dienst auf `https://localhost:5001` lauscht.</span><span class="sxs-lookup"><span data-stu-id="97dd9-150">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="97dd9-151">Die gRPC-Vorlage ist für die Verwendung von [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="97dd9-151">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="97dd9-152">gRPC-Clients müssen zum Aufrufen des Servers HTTPS verwenden.</span><span class="sxs-lookup"><span data-stu-id="97dd9-152">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="97dd9-153">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="97dd9-153">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="97dd9-154">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="97dd9-154">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="97dd9-155">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="97dd9-155">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="97dd9-156">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="97dd9-156">Examine the project files</span></span>

<span data-ttu-id="97dd9-157">*GrpcGreeter*-Projektdateien:</span><span class="sxs-lookup"><span data-stu-id="97dd9-157">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="97dd9-158">*greet.proto* &ndash; die Datei *Protos/greet.proto* definiert den gRPC-Dienst `Greeter` und wird zum Generieren der gRPC-Serverobjekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="97dd9-158">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="97dd9-159">Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="97dd9-159">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="97dd9-160">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="97dd9-160">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="97dd9-161">*appSettings.json* &ndash; enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="97dd9-161">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="97dd9-162">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="97dd9-162">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="97dd9-163">*Program.cs* &ndash; enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="97dd9-163">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="97dd9-164">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="97dd9-164">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="97dd9-165">*Startup.cs* &ndash; enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="97dd9-165">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="97dd9-166">Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="97dd9-166">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="97dd9-167">Erstellen des gRPC-Clients in einer .NET-Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="97dd9-167">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-168">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97dd9-169">Öffnen Sie eine zweite Instanz von Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-169">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="97dd9-170">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Konsolen-App (.NET Core)** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-170">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="97dd9-171">Geben Sie im Textfeld **Name** den Namen **GrpcGreeterClient** ein, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-171">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97dd9-173">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="97dd9-173">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="97dd9-174">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="97dd9-174">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="97dd9-175">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="97dd9-175">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-176">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="97dd9-177">Befolgen Sie die Anweisungen unter [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution), um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="97dd9-177">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="97dd9-178">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="97dd9-178">Add required packages</span></span>

<span data-ttu-id="97dd9-179">Das gRPC-Clientprojekt erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="97dd9-179">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="97dd9-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="97dd9-180">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="97dd9-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="97dd9-181">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="97dd9-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="97dd9-182">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="97dd9-183">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="97dd9-183">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-184">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="97dd9-185">Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.</span><span class="sxs-lookup"><span data-stu-id="97dd9-185">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="97dd9-186">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="97dd9-186">PMC option to install packages</span></span>

* <span data-ttu-id="97dd9-187">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-187">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="97dd9-188">Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `cd GrpcGreeterClient` aus, um zu dem Verzeichnis zu wechseln, in dem sich die *GrpcGreeterClient.csproj*-Dateien befinden.</span><span class="sxs-lookup"><span data-stu-id="97dd9-188">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="97dd9-189">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="97dd9-189">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="97dd9-190">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="97dd9-190">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="97dd9-191">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-191">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="97dd9-192">Wählen Sie die Registerkarte **Durchsuchen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-192">Select the **Browse** tab.</span></span>
* <span data-ttu-id="97dd9-193">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="97dd9-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="97dd9-194">Wählen Sie das Paket **Grpc.Net.Client** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-194">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="97dd9-195">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="97dd9-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="97dd9-197">Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="97dd9-197">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-198">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="97dd9-199">Klicken Sie mit der rechten Maustaste auf den Ordner **Pakete** unter **Lösungspad** > **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="97dd9-199">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="97dd9-200">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="97dd9-200">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="97dd9-201">Wählen Sie das Paket **Grpc.Net.Client** im Ergebnisbereich aus, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-201">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="97dd9-202">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="97dd9-202">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="97dd9-203">Fügen Sie „greet.proto“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="97dd9-203">Add greet.proto</span></span>

* <span data-ttu-id="97dd9-204">Erstellen Sie einen *Protos*-Ordner im gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-204">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="97dd9-205">Kopieren Sie die Datei *Protos\greet.proto* aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-205">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="97dd9-206">Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="97dd9-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="97dd9-208">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="97dd9-210">Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-211">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="97dd9-212">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Extras** > **Datei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-212">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="97dd9-213">Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei *greet.proto* verweist:</span><span class="sxs-lookup"><span data-stu-id="97dd9-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="97dd9-214">Erstellen des Greeter-Clients</span><span class="sxs-lookup"><span data-stu-id="97dd9-214">Create the Greeter client</span></span>

<span data-ttu-id="97dd9-215">Erstellen Sie das Projekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="97dd9-215">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="97dd9-216">Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="97dd9-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="97dd9-217">Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="97dd9-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="97dd9-218">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="97dd9-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="97dd9-219">Der Greeter-Client wird folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="97dd9-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="97dd9-220">Instanziieren eines `HttpClient`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält.</span><span class="sxs-lookup"><span data-stu-id="97dd9-220">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="97dd9-221">Verwenden von `HttpClient` zum Erstellen eines gRPC-Kanals und des Greeter-Clients:</span><span class="sxs-lookup"><span data-stu-id="97dd9-221">Using the `HttpClient` to construct a gRPC channel and the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="97dd9-222">Der Greeter-Client ruft die asynchrone Methode `SayHello` auf.</span><span class="sxs-lookup"><span data-stu-id="97dd9-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="97dd9-223">Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="97dd9-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="97dd9-224">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="97dd9-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="97dd9-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97dd9-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="97dd9-226">Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="97dd9-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="97dd9-227">Drücken Sie im `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Client ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="97dd9-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="97dd9-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97dd9-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="97dd9-229">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="97dd9-229">Start the Greeter service.</span></span>
* <span data-ttu-id="97dd9-230">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="97dd9-230">Start the client.</span></span>


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="97dd9-231">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="97dd9-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="97dd9-232">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="97dd9-232">Start the Greeter service.</span></span>
* <span data-ttu-id="97dd9-233">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="97dd9-233">Start the client.</span></span>

---

<span data-ttu-id="97dd9-234">Der Client sendet eine Begrüßungsnachricht an den Dienst, die den Namen des Diensts enthält: *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="97dd9-234">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="97dd9-235">Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus.</span><span class="sxs-lookup"><span data-stu-id="97dd9-235">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="97dd9-236">Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="97dd9-236">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="97dd9-237">Der gRPC-Dienst zeichnet die Details des erfolgreichen Aufrufs in den Protokollen auf, die an die Eingabeaufforderung ausgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="97dd9-237">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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

> [!NOTE]
> <span data-ttu-id="97dd9-238">Der Code in diesem Artikel erfordert das ASP.NET Core-HTTPS-Entwicklungszertifikat, um den gRPC-Dienst zu sichern.</span><span class="sxs-lookup"><span data-stu-id="97dd9-238">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="97dd9-239">Wenn auf dem Client ein Fehler mit der Meldung `The remote certificate is invalid according to the validation procedure.` auftritt, wird das Entwicklungszertifikat nicht als vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="97dd9-239">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="97dd9-240">Anweisungen zum Beheben dieses Problems finden Sie unter [Einstufen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="97dd9-240">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="97dd9-241">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="97dd9-241">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
