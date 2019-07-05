---
title: Erstellen eines .NET Core-gRPC-Clients und -Servers in ASP.NET Core
author: juntaoluo
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 8507c3dcefeb61a4dd34a1ff967c082d287cf646
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555893"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="31dcc-104">Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31dcc-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="31dcc-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="31dcc-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="31dcc-106">In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.</span><span class="sxs-lookup"><span data-stu-id="31dcc-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="31dcc-107">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="31dcc-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="31dcc-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="31dcc-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="31dcc-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="31dcc-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="31dcc-110">Erstellen Sie einen gRPC-Server.</span><span class="sxs-lookup"><span data-stu-id="31dcc-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="31dcc-111">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="31dcc-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="31dcc-112">Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).</span><span class="sxs-lookup"><span data-stu-id="31dcc-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31dcc-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="31dcc-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="31dcc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="31dcc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="31dcc-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="31dcc-117">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="31dcc-117">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31dcc-119">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-119">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="31dcc-120">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="31dcc-121">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-121">Select **Next**</span></span>
* <span data-ttu-id="31dcc-122">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-122">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="31dcc-123">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="31dcc-123">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="31dcc-124">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-124">Select **Create**</span></span>
* <span data-ttu-id="31dcc-125">Führen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** folgende Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="31dcc-125">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="31dcc-126">Wählen Sie in den Dropdownmenüs **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-126">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="31dcc-127">Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-127">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="31dcc-128">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-128">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="31dcc-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="31dcc-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="31dcc-130">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="31dcc-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="31dcc-131">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="31dcc-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="31dcc-132">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="31dcc-132">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="31dcc-133">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="31dcc-134">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="31dcc-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="31dcc-135">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="31dcc-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="31dcc-136">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-136">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="31dcc-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="31dcc-138">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="31dcc-138">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="31dcc-139">Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="31dcc-140">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="31dcc-140">Open the project</span></span>

<span data-ttu-id="31dcc-141">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie die Datei *GrpcGreeter.sln* aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-141">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="31dcc-142">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="31dcc-142">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31dcc-144">Drücken Sie `Ctrl+F5`, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="31dcc-144">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="31dcc-145">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-145">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="31dcc-146">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-146">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="31dcc-147">Führen Sie das gRPC-Greeter-Projekt *GrpcGreeter* über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-147">Run the gRPC Greeter project *GrpcGreeter* from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="31dcc-148">In den Protokollen wird vermerkt, dass der Dienst auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="31dcc-148">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="31dcc-149">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="31dcc-149">Examine the project files</span></span>

<span data-ttu-id="31dcc-150">*GrpcGreeter*-Projektdateien:</span><span class="sxs-lookup"><span data-stu-id="31dcc-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="31dcc-151">*greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="31dcc-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="31dcc-152">Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="31dcc-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="31dcc-153">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="31dcc-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="31dcc-154">*appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="31dcc-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="31dcc-155">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="31dcc-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="31dcc-156">*Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="31dcc-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="31dcc-157">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="31dcc-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="31dcc-158">*Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="31dcc-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="31dcc-159">Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="31dcc-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="31dcc-160">Erstellen des gRPC-Clients in einer .NET-Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="31dcc-160">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31dcc-162">Öffnen Sie eine zweite Instanz von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="31dcc-162">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="31dcc-163">Wählen Sie **Datei** > **Neu** > **Projekt** aus der Menüleiste aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-163">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="31dcc-164">Wählen Sie im Dialogfeld **Neues Projekt** **Konsolen-App (.NET Core)** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-164">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="31dcc-165">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-165">Select **Next**</span></span>
* <span data-ttu-id="31dcc-166">Geben sie im Textfeld **Name** „GrpcGreeterClient“ ein.</span><span class="sxs-lookup"><span data-stu-id="31dcc-166">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="31dcc-167">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-167">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="31dcc-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="31dcc-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="31dcc-169">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="31dcc-169">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="31dcc-170">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="31dcc-170">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="31dcc-171">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="31dcc-171">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="31dcc-172">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="31dcc-173">Folgen Sie [diesen](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="31dcc-173">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="31dcc-174">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="31dcc-174">Add required packages</span></span>

<span data-ttu-id="31dcc-175">Das gRPC-Clientprojekt erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="31dcc-175">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="31dcc-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="31dcc-176">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="31dcc-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="31dcc-177">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="31dcc-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="31dcc-178">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="31dcc-179">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="31dcc-179">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31dcc-181">Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.</span><span class="sxs-lookup"><span data-stu-id="31dcc-181">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="31dcc-182">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="31dcc-182">PMC option to install packages</span></span>

* <span data-ttu-id="31dcc-183">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-183">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="31dcc-184">Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.</span><span class="sxs-lookup"><span data-stu-id="31dcc-184">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="31dcc-185">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="31dcc-185">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="31dcc-186">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="31dcc-186">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="31dcc-187">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-187">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="31dcc-188">Wählen Sie die Registerkarte **Durchsuchen** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-188">Select the **Browse** tab.</span></span>
* <span data-ttu-id="31dcc-189">Geben Sie **Grpc.Core** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="31dcc-189">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="31dcc-190">Wählen Sie das Paket **Grpc.Core** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-190">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="31dcc-191">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="31dcc-191">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="31dcc-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="31dcc-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="31dcc-193">Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="31dcc-193">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="31dcc-194">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="31dcc-195">Klicken Sie mit der rechten Maustaste auf den Ordner **Pakete** unter **Lösungspad** > **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="31dcc-195">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="31dcc-196">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="31dcc-196">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="31dcc-197">Wählen Sie das Paket **Grpc.Net.Client** im Ergebnisbereich aus, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-197">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="31dcc-198">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="31dcc-198">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="31dcc-199">Fügen Sie „greet.proto“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="31dcc-199">Add greet.proto</span></span>

* <span data-ttu-id="31dcc-200">Erstellen Sie einen **Protos**-Ordner im gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-200">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="31dcc-201">Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-201">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="31dcc-202">Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="31dcc-202">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-203">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="31dcc-204">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-204">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="31dcc-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="31dcc-205">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="31dcc-206">Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-206">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="31dcc-207">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="31dcc-208">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Extras > Datei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-208">Right-click the project and select **Tools > Edit File**.</span></span>

  ---

* <span data-ttu-id="31dcc-209">Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei **greet.proto** verweist:</span><span class="sxs-lookup"><span data-stu-id="31dcc-209">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="31dcc-210">Erstellen des Greeter-Clients</span><span class="sxs-lookup"><span data-stu-id="31dcc-210">Create the Greeter client</span></span>

<span data-ttu-id="31dcc-211">Erstellen Sie das Projekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="31dcc-211">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="31dcc-212">Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="31dcc-212">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="31dcc-213">Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="31dcc-213">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="31dcc-214">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="31dcc-214">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="31dcc-215">Der Greeter-Client wird folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="31dcc-215">The Greeter client is created by:</span></span>

* <span data-ttu-id="31dcc-216">Instanziieren eines `HttpClient`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält.</span><span class="sxs-lookup"><span data-stu-id="31dcc-216">Instantiating an `HttpClient` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="31dcc-217">Verwenden von `HttpClient` zum Erstellen des Greeter-Clients:</span><span class="sxs-lookup"><span data-stu-id="31dcc-217">Using the `HttpClient` to construct the Greeter client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

<span data-ttu-id="31dcc-218">Der Greeter-Client ruft die asynchrone Methode `SayHello` auf.</span><span class="sxs-lookup"><span data-stu-id="31dcc-218">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="31dcc-219">Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="31dcc-219">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="31dcc-220">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="31dcc-220">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="31dcc-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31dcc-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31dcc-222">Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="31dcc-222">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="31dcc-223">Drücken Sie `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="31dcc-223">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="31dcc-224">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="31dcc-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="31dcc-225">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="31dcc-225">Start the Greeter service.</span></span>
* <span data-ttu-id="31dcc-226">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="31dcc-226">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="31dcc-227">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="31dcc-227">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="31dcc-228">Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus.</span><span class="sxs-lookup"><span data-stu-id="31dcc-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="31dcc-229">Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="31dcc-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="31dcc-230">Der gRPC-Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="31dcc-230">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="31dcc-231">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="31dcc-231">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
