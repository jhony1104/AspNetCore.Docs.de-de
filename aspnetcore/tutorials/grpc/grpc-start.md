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
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="98e9d-104">Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98e9d-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="98e9d-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="98e9d-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="98e9d-106">In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.</span><span class="sxs-lookup"><span data-stu-id="98e9d-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="98e9d-107">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="98e9d-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="98e9d-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="98e9d-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="98e9d-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="98e9d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="98e9d-110">Erstellen Sie einen gRPC-Server.</span><span class="sxs-lookup"><span data-stu-id="98e9d-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="98e9d-111">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="98e9d-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="98e9d-112">Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).</span><span class="sxs-lookup"><span data-stu-id="98e9d-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a><span data-ttu-id="98e9d-113">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="98e9d-113">Create a gRPC service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98e9d-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="98e9d-116">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-116">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="98e9d-117">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-117">Select **Next**</span></span>
* <span data-ttu-id="98e9d-118">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-118">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="98e9d-119">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="98e9d-119">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="98e9d-120">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-120">Select **Create**</span></span>
* <span data-ttu-id="98e9d-121">Führen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** folgende Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="98e9d-121">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="98e9d-122">Wählen Sie in den Dropdownmenüs **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-122">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="98e9d-123">Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-123">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="98e9d-124">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-124">Select **Create**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98e9d-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98e9d-125">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="98e9d-126">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="98e9d-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="98e9d-127">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="98e9d-127">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="98e9d-128">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="98e9d-128">Run the following commands:</span></span>

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="98e9d-129">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-129">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="98e9d-130">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="98e9d-130">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="98e9d-131">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="98e9d-131">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="98e9d-132">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-132">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98e9d-133">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98e9d-134">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="98e9d-134">From a terminal, run the following commands:</span></span>

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

<span data-ttu-id="98e9d-135">Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-135">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="98e9d-136">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="98e9d-136">Open the project</span></span>

<span data-ttu-id="98e9d-137">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie die Datei *GrpcGreeter.sln* aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-137">From Visual Studio, select **File > Open**, and then select the *GrpcGreeter.sln* file.</span></span>

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a><span data-ttu-id="98e9d-138">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="98e9d-138">Run the service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-139">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98e9d-140">Drücken Sie STRG+F5, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="98e9d-140">Press Ctrl+F5 to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="98e9d-141">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-141">Visual Studio runs the service in a command prompt.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="98e9d-142">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-142">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="98e9d-143">Führen Sie das gRPC-Greeter-Projekt „GrpcGreeter“ über die Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-143">Run the gRPC Greeter project GrpcGreeter from the command line using `dotnet run`.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="98e9d-144">In den Protokollen wird vermerkt, dass der Dienst auf `http://localhost:50051` lauscht.</span><span class="sxs-lookup"><span data-stu-id="98e9d-144">The logs show the service listening on `http://localhost:50051`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a><span data-ttu-id="98e9d-145">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="98e9d-145">Examine the project files</span></span>

<span data-ttu-id="98e9d-146">gRPCGreeter-Dateien:</span><span class="sxs-lookup"><span data-stu-id="98e9d-146">GrpcGreeter files:</span></span>

* <span data-ttu-id="98e9d-147">*greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="98e9d-147">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="98e9d-148">Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="98e9d-148">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="98e9d-149">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="98e9d-149">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="98e9d-150">*appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="98e9d-150">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="98e9d-151">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="98e9d-151">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="98e9d-152">*Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="98e9d-152">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="98e9d-153">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="98e9d-153">For more information, see <xref:fundamentals/host/web-host>.</span></span>
* <span data-ttu-id="98e9d-154">*Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="98e9d-154">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="98e9d-155">Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="98e9d-155">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="98e9d-156">Erstellen des gRPC-Clients in einer .NET-Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="98e9d-156">Create the gRPC client in a .NET console app</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98e9d-158">Wählen Sie **Datei** > **Neu** > **Projekt** aus der Menüleiste aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-158">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="98e9d-159">Wählen Sie im Dialogfeld **Neues Projekt** **Konsolen-App (.NET Core)** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-159">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="98e9d-160">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-160">Select **Next**</span></span>
* <span data-ttu-id="98e9d-161">Geben sie im Textfeld **Name** „GrpcGreeterClient“ ein.</span><span class="sxs-lookup"><span data-stu-id="98e9d-161">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="98e9d-162">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-162">Select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98e9d-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98e9d-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="98e9d-164">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="98e9d-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="98e9d-165">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="98e9d-165">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="98e9d-166">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="98e9d-166">Run the following commands:</span></span>

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98e9d-167">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98e9d-168">Folgen Sie [diesen](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="98e9d-168">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a><span data-ttu-id="98e9d-169">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="98e9d-169">Add required packages</span></span>

<span data-ttu-id="98e9d-170">Fügen Sie die folgenden Pakete zum gRPC-Clientprojekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="98e9d-170">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="98e9d-171">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), das die C#-API für den C-Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="98e9d-171">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="98e9d-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="98e9d-172">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="98e9d-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="98e9d-173">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="98e9d-174">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="98e9d-174">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98e9d-176">Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.</span><span class="sxs-lookup"><span data-stu-id="98e9d-176">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Package</span></span>

####  <a name="pmc-option-to-install-packages"></a><span data-ttu-id="98e9d-177">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="98e9d-177">PMC option to install packages</span></span>

* <span data-ttu-id="98e9d-178">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-178">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="98e9d-179">Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.</span><span class="sxs-lookup"><span data-stu-id="98e9d-179">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="98e9d-180">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="98e9d-180">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Core
Install-Package Grpc.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="98e9d-181">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="98e9d-181">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="98e9d-182">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-182">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="98e9d-183">Wählen Sie die Registerkarte **Durchsuchen** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-183">Select the **Browse** tab.</span></span>
* <span data-ttu-id="98e9d-184">Geben Sie **Grpc.Core** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="98e9d-184">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="98e9d-185">Wählen Sie das Paket **Grpc.Core** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-185">Select the **Grpc.Core** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="98e9d-186">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="98e9d-186">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98e9d-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98e9d-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98e9d-188">Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="98e9d-188">Run the following commands from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98e9d-189">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="98e9d-190">Klicken Sie mit der rechten Maustaste auf den Ordner **Pakete** unter **Lösungspad** > **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-190">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="98e9d-191">Geben Sie **Grpc.Core** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="98e9d-191">Enter **Grpc.Core** in the search box.</span></span>
* <span data-ttu-id="98e9d-192">Wählen Sie das Paket **Grpc.Core** aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="98e9d-192">Select the **Grpc.Core** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="98e9d-193">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="98e9d-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="98e9d-194">Fügen Sie „greet.proto“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="98e9d-194">Add greet.proto</span></span>

* <span data-ttu-id="98e9d-195">Erstellen Sie einen **Protos**-Ordner im gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-195">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="98e9d-196">Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-196">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="98e9d-197">Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="98e9d-197">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-198">Visual Studio</span></span>](#tab/visual-studio) 

  <span data-ttu-id="98e9d-199">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **GrpcGreeterClient.csproj bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-199">Right-click the project and select the **Edit GrpcGreeterClient.csproj**.</span></span>

  # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98e9d-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98e9d-200">Visual Studio Code</span></span>](#tab/visual-studio-code) 

  <span data-ttu-id="98e9d-201">Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-201">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="98e9d-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="98e9d-203">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Extras > Datei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-203">Right click the project and select **Tools > Edit File**.</span></span>

  ------

* <span data-ttu-id="98e9d-204">Fügen Sie der `<Protobuf>`-Elementgruppe aus der GrpcGreeterClient-Projektdatei die Datei **greet.proto** hinzu:</span><span class="sxs-lookup"><span data-stu-id="98e9d-204">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

<span data-ttu-id="98e9d-205">Kompilieren Sie das Clientprojekt, um die Generierung der C#-Clientobjekte auszulösen.</span><span class="sxs-lookup"><span data-stu-id="98e9d-205">Build the client project to trigger the generation of the C# client assets.</span></span>

### <a name="create-the-greater-client"></a><span data-ttu-id="98e9d-206">Erstellen des Greeterclients</span><span class="sxs-lookup"><span data-stu-id="98e9d-206">Create the greater client</span></span>

<span data-ttu-id="98e9d-207">Erstellen Sie das Projekt, um die Typen im **Greeter**-Namespace erstellen zu können.</span><span class="sxs-lookup"><span data-stu-id="98e9d-207">Build the project to create the types in the **Greeter** namespace.</span></span> <span data-ttu-id="98e9d-208">Die `Greeter`-Typen werden vom Buildprozess automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="98e9d-208">The `Greeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="98e9d-209">Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="98e9d-209">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="98e9d-210">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="98e9d-210">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="98e9d-211">Der Greeterclient wird folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="98e9d-211">The greater client is created by:</span></span>

* <span data-ttu-id="98e9d-212">Instanziieren eines `Channel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält</span><span class="sxs-lookup"><span data-stu-id="98e9d-212">Instantiating a `Channel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="98e9d-213">Verwenden des `Channel`-Objekts zum Erstellen des Greeterclients:</span><span class="sxs-lookup"><span data-stu-id="98e9d-213">Using the `Channel` to construct the greater client:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-6)]

<span data-ttu-id="98e9d-214">Der Greeterclient ruft die asynchrone Methode `SayHello` auf.</span><span class="sxs-lookup"><span data-stu-id="98e9d-214">The greater client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="98e9d-215">Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="98e9d-215">The result of the `SayHello` call is displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

<span data-ttu-id="98e9d-216">Halten Sie das vom Client verwendete `Channel`-Objekt an, nachdem die Vorgänge zum Freigeben aller Ressourcen abgeschlossen sind:</span><span class="sxs-lookup"><span data-stu-id="98e9d-216">Shut down the `Channel` used by the client when operations have finished to release all resources.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="98e9d-217">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="98e9d-217">Test the gRPC client with the gRPC Greeter service</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98e9d-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98e9d-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98e9d-219">Drücken Sie im Greeterdienst STRG+F5, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="98e9d-219">In the Greeter service, press Ctrl+F5 to start the server without the debugger.</span></span>
* <span data-ttu-id="98e9d-220">Drücken Sie im GrpcGreeterClient-Projekt STRG+F5, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="98e9d-220">In the GrpcGreeterClient project, press Ctrl+F5 to start the server without the debugger.</span></span>

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="98e9d-221">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="98e9d-221">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="98e9d-222">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="98e9d-222">Start the Greeter service.</span></span>
* <span data-ttu-id="98e9d-223">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="98e9d-223">Start the client.</span></span>

<!-- End of combined VS/Mac tabs -->

---

<span data-ttu-id="98e9d-224">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="98e9d-224">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="98e9d-225">Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus.</span><span class="sxs-lookup"><span data-stu-id="98e9d-225">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="98e9d-226">Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="98e9d-226">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="98e9d-227">Der gRPC-Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="98e9d-227">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="next-steps"></a><span data-ttu-id="98e9d-228">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="98e9d-228">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
