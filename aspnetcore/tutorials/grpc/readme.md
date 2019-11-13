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
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463045"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="b68a9-102">Erstellen eines gRPC-Clients und -Servers in ASP.NET Core 3.0 mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b68a9-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="b68a9-103">In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.</span><span class="sxs-lookup"><span data-stu-id="b68a9-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="b68a9-104">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="b68a9-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="b68a9-105">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b68a9-105">In this tutorial, you;</span></span>

* <span data-ttu-id="b68a9-106">Erstellen Sie einen gRPC-Server.</span><span class="sxs-lookup"><span data-stu-id="b68a9-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="b68a9-107">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="b68a9-107">Create a gRPC client.</span></span>
* <span data-ttu-id="b68a9-108">Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).</span><span class="sxs-lookup"><span data-stu-id="b68a9-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="b68a9-109">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="b68a9-109">Create a gRPC service</span></span>

* <span data-ttu-id="b68a9-110">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b68a9-111">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b68a9-112">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-112">Select **Next**</span></span>
* <span data-ttu-id="b68a9-113">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="b68a9-114">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="b68a9-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="b68a9-115">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-115">Select **Create**</span></span>
* <span data-ttu-id="b68a9-116">Führen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** folgende Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="b68a9-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="b68a9-117">Wählen Sie in den Dropdownmenüs **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="b68a9-118">Wählen Sie die Vorlage **gRPC Service** (gRPC-Dienst) aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="b68a9-119">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="b68a9-120">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="b68a9-120">Run the service</span></span>

* <span data-ttu-id="b68a9-121">Drücken Sie `Ctrl+F5`, um den gRPC-Dienst ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b68a9-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="b68a9-122">Visual Studio führt den Dienst über die Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="b68a9-123">In den Protokollen wird vermerkt, dass der Dienst auf `https://localhost:5001` lauscht.</span><span class="sxs-lookup"><span data-stu-id="b68a9-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="b68a9-124">Die gRPC-Vorlage ist für die Verwendung von [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b68a9-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="b68a9-125">gRPC-Clients müssen zum Aufrufen des Servers HTTPS verwenden.</span><span class="sxs-lookup"><span data-stu-id="b68a9-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="b68a9-126">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="b68a9-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="b68a9-127">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b68a9-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="b68a9-128">Weitere Informationen finden Sie unter [gRPC und ASP.NET Core unter macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="b68a9-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="b68a9-129">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="b68a9-129">Examine the project files</span></span>

<span data-ttu-id="b68a9-130">*GrpcGreeter*-Projektdateien:</span><span class="sxs-lookup"><span data-stu-id="b68a9-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="b68a9-131">*greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="b68a9-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="b68a9-132">Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="b68a9-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="b68a9-133">*Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="b68a9-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="b68a9-134">*appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="b68a9-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="b68a9-135">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b68a9-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="b68a9-136">*Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="b68a9-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="b68a9-137">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="b68a9-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="b68a9-138">*Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="b68a9-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="b68a9-139">Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b68a9-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="b68a9-140">Erstellen des gRPC-Clients in einer .NET-Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="b68a9-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="b68a9-141">Öffnen Sie eine zweite Instanz von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b68a9-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="b68a9-142">Wählen Sie **Datei** > **Neu** > **Projekt** aus der Menüleiste aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="b68a9-143">Wählen Sie im Dialogfeld **Neues Projekt** **Konsolen-App (.NET Core)** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="b68a9-144">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-144">Select **Next**</span></span>
* <span data-ttu-id="b68a9-145">Geben sie im Textfeld **Name** „GrpcGreeterClient“ ein.</span><span class="sxs-lookup"><span data-stu-id="b68a9-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="b68a9-146">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="b68a9-147">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="b68a9-147">Add required packages</span></span>

<span data-ttu-id="b68a9-148">Das gRPC-Clientprojekt erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="b68a9-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="b68a9-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="b68a9-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="b68a9-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="b68a9-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="b68a9-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="b68a9-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="b68a9-152">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="b68a9-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="b68a9-153">Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.</span><span class="sxs-lookup"><span data-stu-id="b68a9-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="b68a9-154">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="b68a9-154">PMC option to install packages</span></span>

* <span data-ttu-id="b68a9-155">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="b68a9-156">Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.</span><span class="sxs-lookup"><span data-stu-id="b68a9-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="b68a9-157">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b68a9-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="b68a9-158">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="b68a9-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="b68a9-159">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="b68a9-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="b68a9-160">Wählen Sie die Registerkarte **Durchsuchen** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="b68a9-161">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="b68a9-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="b68a9-162">Wählen Sie das Paket **Grpc.Net.Client** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="b68a9-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="b68a9-163">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="b68a9-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="b68a9-164">Fügen Sie „greet.proto“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="b68a9-164">Add greet.proto</span></span>

* <span data-ttu-id="b68a9-165">Erstellen Sie einen **Protos**-Ordner im gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="b68a9-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="b68a9-166">Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="b68a9-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="b68a9-167">Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="b68a9-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="b68a9-168">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="b68a9-169">Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei **greet.proto** verweist:</span><span class="sxs-lookup"><span data-stu-id="b68a9-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="b68a9-170">Erstellen des Greeter-Clients</span><span class="sxs-lookup"><span data-stu-id="b68a9-170">Create the Greeter client</span></span>

<span data-ttu-id="b68a9-171">Erstellen Sie das Projekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b68a9-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="b68a9-172">Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="b68a9-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="b68a9-173">Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b68a9-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="b68a9-174">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="b68a9-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="b68a9-175">Der Greeter-Client wird folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="b68a9-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="b68a9-176">Instanziieren eines `GrpcChannel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält</span><span class="sxs-lookup"><span data-stu-id="b68a9-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="b68a9-177">Verwenden von `GrpcChannel` zum Erstellen des Greeter-Clients.</span><span class="sxs-lookup"><span data-stu-id="b68a9-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="b68a9-178">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="b68a9-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="b68a9-179">Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="b68a9-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="b68a9-180">Drücken Sie im `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Client ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="b68a9-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="b68a9-181">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="b68a9-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="b68a9-182">Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus.</span><span class="sxs-lookup"><span data-stu-id="b68a9-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="b68a9-183">Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b68a9-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="b68a9-184">Der gRPC-Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b68a9-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="b68a9-185">Hilfe aus der Dokumentation und nächste Schritte für gRPC</span><span class="sxs-lookup"><span data-stu-id="b68a9-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="b68a9-186">Einführung in gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b68a9-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="b68a9-187">gRPC-Dienste mit C#</span><span class="sxs-lookup"><span data-stu-id="b68a9-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="b68a9-188">Migrieren von gRPC-Diensten von C-core zu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b68a9-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
