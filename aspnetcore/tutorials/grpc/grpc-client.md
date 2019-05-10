---
title: 'Tutorial: Erstellen eines .NET Core-Clients'
author: juntaoluo
description: In dieser Tutorialreihe wird beschrieben, wie Sie einen gRPC-Dienst in ASP.NET Core erstellen. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212639"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="a63d5-104">Tutorial: Erstellen eines .NET Core-Clients</span><span class="sxs-lookup"><span data-stu-id="a63d5-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="a63d5-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="a63d5-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="a63d5-106">In diesem Tutorial wird gezeigt, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client erstellen können, der mit gRPC-Diensten kommunizieren kann.</span><span class="sxs-lookup"><span data-stu-id="a63d5-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="a63d5-107">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="a63d5-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="a63d5-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="a63d5-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a63d5-109">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="a63d5-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="a63d5-110">Führen Sie den Dienst mit dem gRPC-Greeter-Dienst aus, der im vorherigen Tutorial erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a63d5-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="a63d5-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="a63d5-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="a63d5-112">Erstellen einer .NET-Konsolenanwendung</span><span class="sxs-lookup"><span data-stu-id="a63d5-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a63d5-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a63d5-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a63d5-114">Folgen Sie [diesen](/dotnet/core/tutorials/with-visual-studio) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-114">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a63d5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a63d5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a63d5-116">Folgen Sie [diesen](/dotnet/core/tutorials/with-visual-studio-code) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-116">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a63d5-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a63d5-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a63d5-118">Folgen Sie [diesen](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Anweisungen, um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-118">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="a63d5-119">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="a63d5-119">Add required packages</span></span>

<span data-ttu-id="a63d5-120">Fügen Sie die folgenden Pakete zum gRPC-Clientprojekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="a63d5-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="a63d5-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), das die C#-API für den C-Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="a63d5-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="a63d5-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="a63d5-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="a63d5-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="a63d5-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="a63d5-124">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="a63d5-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="a63d5-125">Pakete können mit den folgenden Vorgehensweisen hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a63d5-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a63d5-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a63d5-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="a63d5-127">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="a63d5-127">PMC option to install packages</span></span>

* <span data-ttu-id="a63d5-128">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="a63d5-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="a63d5-129">Navigieren Sie vom Fenster **Paket-Manager-Konsole** zu dem Verzeichnis, in dem sich die Datei *GrpcGreeterClient.csproj* befindet.</span><span class="sxs-lookup"><span data-stu-id="a63d5-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="a63d5-130">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="a63d5-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="a63d5-131">Wiederholen Sie das `Install-Package` für Google.Protobuf und Grpc.Tools.</span><span class="sxs-lookup"><span data-stu-id="a63d5-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="a63d5-132">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="a63d5-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="a63d5-133">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="a63d5-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="a63d5-134">Legen Sie die **Paketquelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="a63d5-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="a63d5-135">Geben Sie „Grpc.Core“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="a63d5-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="a63d5-136">Wählen Sie das Paket „Grpc.Core“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="a63d5-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="a63d5-137">Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.</span><span class="sxs-lookup"><span data-stu-id="a63d5-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a63d5-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a63d5-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a63d5-139">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="a63d5-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

<span data-ttu-id="a63d5-140">Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.</span><span class="sxs-lookup"><span data-stu-id="a63d5-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a63d5-141">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a63d5-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a63d5-142">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.</span><span class="sxs-lookup"><span data-stu-id="a63d5-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="a63d5-143">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="a63d5-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="a63d5-144">Geben Sie „Grpc.Core“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="a63d5-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="a63d5-145">Wählen Sie das Paket „Grpc.Core“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a63d5-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="a63d5-146">Wiederholen Sie dies für Google.Protobuf und Grpc.Tools.</span><span class="sxs-lookup"><span data-stu-id="a63d5-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="a63d5-147">Hinzufügen der„greet.proto“-Datei</span><span class="sxs-lookup"><span data-stu-id="a63d5-147">Add the greet.proto file</span></span>

<span data-ttu-id="a63d5-148">Kopieren Sie die Datei **Protos\greet.proto** aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="a63d5-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="a63d5-149">Fügen Sie der `<Protobuf>`-Elementgruppe aus der GrpcGreeterClient-Projektdatei die Datei **greet.proto** hinzu:</span><span class="sxs-lookup"><span data-stu-id="a63d5-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="a63d5-150">Sie können die GrpcGreeterClient-Projektdatei durch einen Rechtsklick auf das Projekt und Auswählen der Option **Edit GrpcGreeterClient.csproj** (GrpcGreeterClient.csproj bearbeiten) aus dem Dropdownmenü öffnen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![neue ASP.NET Core-Webanwendung](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="a63d5-152">Alternativ können Sie zum GrpcGreeterClient-Verzeichnis navigieren und die Datei `GrpcGreeterClient.csproj` in Ihrem bevorzugten Editor bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="a63d5-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="a63d5-153">Das `GrpcServices="Client"`-Attribut wird hinzugefügt, sodass nur die C#-Clientobjekte für die enthaltene Protobuf-Datei generiert werden.</span><span class="sxs-lookup"><span data-stu-id="a63d5-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="a63d5-154">Kompilieren Sie das Clientprojekt, um die Generierung der C#-Clientobjekte auszulösen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="a63d5-155">Erstellen eines „GreeterClient“ und Aufrufen des unären Aufrufs „SayHello“</span><span class="sxs-lookup"><span data-stu-id="a63d5-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="a63d5-156">Fügen Sie der `Main`-Methode der `Program.cs`-Datei aus dem gRPC-Clientprojekt den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="a63d5-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="a63d5-157">Für den Zugriff auf die erforderlichen Typen sind die folgenden using-Anweisungen erforderlich:</span><span class="sxs-lookup"><span data-stu-id="a63d5-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="a63d5-158">Der GreeterClient wird erstellt, indem ein `Channel` instanziiert wird, der die Informationen zum Herstellen der Verbindung mit dem gRPC-Dienst enthält, und zum Erstellen des `GreeterClient` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a63d5-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="a63d5-159">Der GreeterClient enthält den unären Aufruf `SayHello`, der asynchron aufgerufen werden kann:</span><span class="sxs-lookup"><span data-stu-id="a63d5-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="a63d5-160">Die Ergebnisse des Aufrufs `SayHello` werden in `reply` gespeichert, was dann angezeigt werden kann:</span><span class="sxs-lookup"><span data-stu-id="a63d5-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="a63d5-161">Der vom Client verwendete `Channel` sollte deaktiviert werden, nachdem die Vorgänge zum Freigeben aller Ressourcen abgeschlossen sind:</span><span class="sxs-lookup"><span data-stu-id="a63d5-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="a63d5-162">Bevor die Typen im **Greeter**-Namespace aufgelöst werden können, müssen Sie das Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="a63d5-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="a63d5-163">Diese Typen werden automatisch während des Buildvorgangs generiert und sind erst verfügbar, wenn ein Build ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a63d5-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="a63d5-164">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="a63d5-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a63d5-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a63d5-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a63d5-166">Stellen Sie sicher, dass der im vorherigen Tutorial erstellte Greeter-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a63d5-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="a63d5-167">Sobald der Dienst ausgeführt wird, kehren Sie zum **GrpcGreeterClient**-Projekt zurück und legen Sie dieses als Startprojekt fest.</span><span class="sxs-lookup"><span data-stu-id="a63d5-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="a63d5-168">Drücken Sie STRG+F5, um die Ausführung des Clients ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="a63d5-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="a63d5-169">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="a63d5-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="a63d5-170">Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a63d5-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/client.png)

  <span data-ttu-id="a63d5-172">Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a63d5-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![neue ASP.NET Core-Webanwendung](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a63d5-174">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a63d5-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a63d5-175">Stellen Sie sicher, dass der im vorherigen Tutorial erstellte Greeter-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a63d5-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="a63d5-176">Führen Sie das Clientprojekt „GrpcGreeter.Client“ über die separate Befehlszeile mit `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="a63d5-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="a63d5-177">Der Client sendet an den Dienst eine Begrüßungsnachricht, die dessen Namen („GreeterClient“) enthält.</span><span class="sxs-lookup"><span data-stu-id="a63d5-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="a63d5-178">Daraufhin antwortet der Dienst mit der Nachricht „Hello GreeterClient“, die über die Eingabeaufforderung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a63d5-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="a63d5-179">Der Dienst erfasst die Details des erfolgreichen Aufrufs in den Protokollen, die über die Eingabeaufforderung ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a63d5-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="a63d5-180">Ansehen der gRPC-Projektdateien</span><span class="sxs-lookup"><span data-stu-id="a63d5-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="a63d5-181">gRPC-Clientdatei „GrpcGreeterClient“:</span><span class="sxs-lookup"><span data-stu-id="a63d5-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="a63d5-182">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="a63d5-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a63d5-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a63d5-183">Additional resources</span></span>

<span data-ttu-id="a63d5-184">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="a63d5-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a63d5-185">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="a63d5-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="a63d5-186">Führen Sie den Dienst mit dem gRPC-Greeter-Dienst aus, der im vorherigen Tutorial erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a63d5-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="a63d5-187">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="a63d5-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a63d5-188">Zurück: Erstellen eines gRPC-Greeter-Diensts</span><span class="sxs-lookup"><span data-stu-id="a63d5-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
