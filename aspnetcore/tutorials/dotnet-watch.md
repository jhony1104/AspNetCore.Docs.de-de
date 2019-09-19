---
title: Entwickeln von ASP.NET Core-Apps mit einem Dateiwatcher
author: rick-anderson
description: In diesem Tutorial wird erläutert, wie Sie das Dateiwatcher-Tool (dotnet watch) der .NET Core-CLI in einer ASP.NET Core-App installieren und verwenden.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 5462f89a3b5a257ed0a6a8439efb077653fb14f6
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082250"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="9194e-103">Entwickeln von ASP.NET Core-Apps mit einem Dateiwatcher</span><span class="sxs-lookup"><span data-stu-id="9194e-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="9194e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="9194e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="9194e-105">`dotnet watch` ist ein Tool, das einen [.NET Core-CLI](/dotnet/core/tools)-Befehl ausführt, wenn sich Quelldateien ändern.</span><span class="sxs-lookup"><span data-stu-id="9194e-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="9194e-106">Eine Dateiänderung kann beispielsweise eine Kompilierung, Testausführung oder Bereitstellung auslösen.</span><span class="sxs-lookup"><span data-stu-id="9194e-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="9194e-107">In diesem Tutorial wird eine vorhandene Web-API mit zwei Endpunkten verwendet: der eine gibt eine Summe zurück, der andere ein Produkt.</span><span class="sxs-lookup"><span data-stu-id="9194e-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="9194e-108">Die Produktmethode enthält einen Fehler, der in diesem Tutorial behoben wird.</span><span class="sxs-lookup"><span data-stu-id="9194e-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="9194e-109">Laden Sie die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample) herunter.</span><span class="sxs-lookup"><span data-stu-id="9194e-109">Download the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="9194e-110">Sie enthält zwei Projekte: *WebApp* (eine ASP.NET Core-Web-API) und *WebAppTests* (Komponententest für die Web-API).</span><span class="sxs-lookup"><span data-stu-id="9194e-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="9194e-111">Navigieren Sie in einer Befehlsshell zum Ordner *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="9194e-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="9194e-112">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9194e-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="9194e-113">Sie können `dotnet run --project <PROJECT>` verwenden, um ein auszuführendes Projekt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9194e-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="9194e-114">Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet run --project WebApp` ausführen, wird auch das Projekt *WebApp* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9194e-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="9194e-115">Die Konsolenausgabe zeigt Meldungen ähnlich der folgenden (die angibt, dass die App ausgeführt wird und auf Anforderungen wartet):</span><span class="sxs-lookup"><span data-stu-id="9194e-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="9194e-116">Navigieren Sie in einem Webbrowser zu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="9194e-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="9194e-117">Es sollten die Ergebnisse von `9` angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-117">You should see the result of `9`.</span></span>

<span data-ttu-id="9194e-118">Navigieren Sie zur Produkt-API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="9194e-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="9194e-119">Es wird nicht wie erwartet `20` sondern `9` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9194e-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="9194e-120">Dieses Problem wird später im Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="9194e-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="9194e-121">Hinzufügen von `dotnet watch` zu einem Projekt</span><span class="sxs-lookup"><span data-stu-id="9194e-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="9194e-122">Das Dateiwatcher-Tool `dotnet watch` ist im Lieferumfang von .NET Core SDK-Version 2.1.300 enthalten.</span><span class="sxs-lookup"><span data-stu-id="9194e-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="9194e-123">Wenn Sie eine frühere Version von .NET Core SDK verwenden, sind die folgenden Schritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9194e-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="9194e-124">Fügen Sie der *CSPROJ*-Datei einen `Microsoft.DotNet.Watcher.Tools`-Paketverweis hinzu:</span><span class="sxs-lookup"><span data-stu-id="9194e-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="9194e-125">Installieren Sie das `Microsoft.DotNet.Watcher.Tools`-Paket, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="9194e-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="9194e-126">Ausführen von .NET Core-CLI-Befehlen mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="9194e-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="9194e-127">Jeder [.NET Core-CLI-Befehl](/dotnet/core/tools#cli-commands) kann mit `dotnet watch` ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="9194e-128">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9194e-128">For example:</span></span>

| <span data-ttu-id="9194e-129">Befehl</span><span class="sxs-lookup"><span data-stu-id="9194e-129">Command</span></span> | <span data-ttu-id="9194e-130">Befehl mit watch</span><span class="sxs-lookup"><span data-stu-id="9194e-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="9194e-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="9194e-131">dotnet run</span></span> | <span data-ttu-id="9194e-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="9194e-132">dotnet watch run</span></span> |
| <span data-ttu-id="9194e-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="9194e-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="9194e-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="9194e-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="9194e-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="9194e-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="9194e-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="9194e-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="9194e-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="9194e-137">dotnet test</span></span> | <span data-ttu-id="9194e-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="9194e-138">dotnet watch test</span></span> |

<span data-ttu-id="9194e-139">Führen Sie `dotnet watch run` im Ordner *WebApp* aus.</span><span class="sxs-lookup"><span data-stu-id="9194e-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="9194e-140">Die Konsolenausgabe gibt an, dass `watch` gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="9194e-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="9194e-141">Sie können `dotnet watch --project <PROJECT>` verwenden, um ein zu überwachendes Projekt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9194e-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="9194e-142">Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet watch --project WebApp run` ausführen, wird auch das Projekt *WebApp* ausgeführt und überwacht.</span><span class="sxs-lookup"><span data-stu-id="9194e-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="9194e-143">Vornehmen von Änderungen mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="9194e-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="9194e-144">Stellen Sie sicher, dass `dotnet watch` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9194e-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="9194e-145">Korrigieren Sie den Fehler in der `Product`-Methode von *MathController.cs* so, dass das Produkt und nicht die Summe zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="9194e-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="9194e-146">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="9194e-146">Save the file.</span></span> <span data-ttu-id="9194e-147">Die Konsolenausgabe zeigt an, dass `dotnet watch` eine Dateiänderung erkannt und die App neu gestartet hat.</span><span class="sxs-lookup"><span data-stu-id="9194e-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="9194e-148">Vergewissern Sie sich, dass `http://localhost:<port number>/api/math/product?a=4&b=5` das richtige Ergebnis zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9194e-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="9194e-149">Ausführen von Tests mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="9194e-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="9194e-150">Ändern Sie die `Product`-Methode von *MathController.cs* so, dass wieder die Summe zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="9194e-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="9194e-151">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="9194e-151">Save the file.</span></span>
1. <span data-ttu-id="9194e-152">Navigieren Sie in einer Befehlsshell zum Ordner *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="9194e-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="9194e-153">Führen Sie [dotnet restore](/dotnet/core/tools/dotnet-restore) aus.</span><span class="sxs-lookup"><span data-stu-id="9194e-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="9194e-154">Führen Sie aus `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="9194e-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="9194e-155">In der Ausgabe wird angezeigt, dass ein Test fehlgeschlagen ist und der Watcher auf Dateiänderungen wartet:</span><span class="sxs-lookup"><span data-stu-id="9194e-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="9194e-156">Ändern Sie den Code der `Product`-Methode so, dass das Produkt zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="9194e-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="9194e-157">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="9194e-157">Save the file.</span></span>

<span data-ttu-id="9194e-158">`dotnet watch` erkennt die Dateiänderung und führt die Tests erneut aus.</span><span class="sxs-lookup"><span data-stu-id="9194e-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="9194e-159">Die Konsolenausgabe zeigt, dass die Tests bestanden wurden.</span><span class="sxs-lookup"><span data-stu-id="9194e-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="9194e-160">Anpassen der Liste mit den zu überwachenden Dateien</span><span class="sxs-lookup"><span data-stu-id="9194e-160">Customize files list to watch</span></span>

<span data-ttu-id="9194e-161">Standardmäßig überwacht `dotnet-watch` alle Dateien mit den folgenden Globmustern:</span><span class="sxs-lookup"><span data-stu-id="9194e-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="9194e-162">Durch Bearbeiten der *CSPROJ*-Datei können der Watchlist weitere Elemente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="9194e-163">Dabei können die Elemente einzeln oder mithilfe von Globmustern angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="9194e-164">Abwählen von zu überwachenden Dateien</span><span class="sxs-lookup"><span data-stu-id="9194e-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="9194e-165">Sie können `dotnet-watch` so konfigurieren, dass die Standardeinstellungen ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="9194e-166">Wenn Sie bestimmte Dateien ignorieren möchten, fügen Sie der Definition eines Elements in der *CSPROJ*-Datei das `Watch="false"`-Attribut hinzu:</span><span class="sxs-lookup"><span data-stu-id="9194e-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="9194e-167">Benutzerdefinierte Überwachungsprojekte</span><span class="sxs-lookup"><span data-stu-id="9194e-167">Custom watch projects</span></span>

<span data-ttu-id="9194e-168">`dotnet-watch` ist nicht auf C#-Projekte beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9194e-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="9194e-169">Sie können für unterschiedliche Szenarios benutzerdefinierte Überwachungsprojekte erstellen.</span><span class="sxs-lookup"><span data-stu-id="9194e-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="9194e-170">Betrachten Sie das folgende Projektlayout:</span><span class="sxs-lookup"><span data-stu-id="9194e-170">Consider the following project layout:</span></span>

* <span data-ttu-id="9194e-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="9194e-171">**test/**</span></span>
  * <span data-ttu-id="9194e-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="9194e-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="9194e-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="9194e-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="9194e-174">Wenn beide Projekte überwacht werden sollen, erstellen Sie eine benutzerdefinierte Projektdatei, die so konfiguriert ist, dass beide Projekte überwacht werden:</span><span class="sxs-lookup"><span data-stu-id="9194e-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="9194e-175">Wechseln Sie zum Starten der Dateiüberwachung in beiden Projekten zum *test*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="9194e-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="9194e-176">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9194e-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="9194e-177">VSTest wird ausgeführt, wenn in einer beliebigen Datei in einem der Testprojekte Änderungen vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="9194e-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="9194e-178">`dotnet-watch` in GitHub</span><span class="sxs-lookup"><span data-stu-id="9194e-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="9194e-179">`dotnet-watch` ist Teil des GitHub-[Aspnet/AspNetCore-Repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="9194e-179">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
