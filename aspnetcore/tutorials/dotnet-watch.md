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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Entwickeln von ASP.NET Core-Apps mit einem Dateiwatcher

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` ist ein Tool, das einen [.NET Core-CLI](/dotnet/core/tools)-Befehl ausführt, wenn sich Quelldateien ändern. Eine Dateiänderung kann beispielsweise eine Kompilierung, Testausführung oder Bereitstellung auslösen.

In diesem Tutorial wird eine vorhandene Web-API mit zwei Endpunkten verwendet: der eine gibt eine Summe zurück, der andere ein Produkt. Die Produktmethode enthält einen Fehler, der in diesem Tutorial behoben wird.

Laden Sie die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample) herunter. Sie enthält zwei Projekte: *WebApp* (eine ASP.NET Core-Web-API) und *WebAppTests* (Komponententest für die Web-API).

Navigieren Sie in einer Befehlsshell zum Ordner *WebApp*. Führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet run
```

> [!NOTE]
> Sie können `dotnet run --project <PROJECT>` verwenden, um ein auszuführendes Projekt anzugeben. Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet run --project WebApp` ausführen, wird auch das Projekt *WebApp* ausgeführt.

Die Konsolenausgabe zeigt Meldungen ähnlich der folgenden (die angibt, dass die App ausgeführt wird und auf Anforderungen wartet):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Navigieren Sie in einem Webbrowser zu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Es sollten die Ergebnisse von `9` angezeigt werden.

Navigieren Sie zur Produkt-API (`http://localhost:<port number>/api/math/product?a=4&b=5`). Es wird nicht wie erwartet `20` sondern `9` zurückgegeben. Dieses Problem wird später im Tutorial behoben.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Hinzufügen von `dotnet watch` zu einem Projekt

Das Dateiwatcher-Tool `dotnet watch` ist im Lieferumfang von .NET Core SDK-Version 2.1.300 enthalten. Wenn Sie eine frühere Version von .NET Core SDK verwenden, sind die folgenden Schritte erforderlich.

1. Fügen Sie der *CSPROJ*-Datei einen `Microsoft.DotNet.Watcher.Tools`-Paketverweis hinzu:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Installieren Sie das `Microsoft.DotNet.Watcher.Tools`-Paket, indem Sie den folgenden Befehl ausführen:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Ausführen von .NET Core-CLI-Befehlen mit `dotnet watch`

Jeder [.NET Core-CLI-Befehl](/dotnet/core/tools#cli-commands) kann mit `dotnet watch` ausgeführt werden. Beispiel:

| Befehl | Befehl mit watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

Führen Sie `dotnet watch run` im Ordner *WebApp* aus. Die Konsolenausgabe gibt an, dass `watch` gestartet wurde.

> [!NOTE]
> Sie können `dotnet watch --project <PROJECT>` verwenden, um ein zu überwachendes Projekt anzugeben. Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet watch --project WebApp run` ausführen, wird auch das Projekt *WebApp* ausgeführt und überwacht.

## <a name="make-changes-with-dotnet-watch"></a>Vornehmen von Änderungen mit `dotnet watch`

Stellen Sie sicher, dass `dotnet watch` ausgeführt wird.

Korrigieren Sie den Fehler in der `Product`-Methode von *MathController.cs* so, dass das Produkt und nicht die Summe zurückgegeben wird:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Speichern Sie die Datei. Die Konsolenausgabe zeigt an, dass `dotnet watch` eine Dateiänderung erkannt und die App neu gestartet hat.

Vergewissern Sie sich, dass `http://localhost:<port number>/api/math/product?a=4&b=5` das richtige Ergebnis zurückgibt.

## <a name="run-tests-using-dotnet-watch"></a>Ausführen von Tests mit `dotnet watch`

1. Ändern Sie die `Product`-Methode von *MathController.cs* so, dass wieder die Summe zurückgegeben wird. Speichern Sie die Datei.
1. Navigieren Sie in einer Befehlsshell zum Ordner *WebAppTests*.
1. Führen Sie [dotnet restore](/dotnet/core/tools/dotnet-restore) aus.
1. Führen Sie aus `dotnet watch test`. In der Ausgabe wird angezeigt, dass ein Test fehlgeschlagen ist und der Watcher auf Dateiänderungen wartet:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Ändern Sie den Code der `Product`-Methode so, dass das Produkt zurückgegeben wird. Speichern Sie die Datei.

`dotnet watch` erkennt die Dateiänderung und führt die Tests erneut aus. Die Konsolenausgabe zeigt, dass die Tests bestanden wurden.

## <a name="customize-files-list-to-watch"></a>Anpassen der Liste mit den zu überwachenden Dateien

Standardmäßig überwacht `dotnet-watch` alle Dateien mit den folgenden Globmustern:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Durch Bearbeiten der *CSPROJ*-Datei können der Watchlist weitere Elemente hinzugefügt werden. Dabei können die Elemente einzeln oder mithilfe von Globmustern angegeben werden.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Abwählen von zu überwachenden Dateien

Sie können `dotnet-watch` so konfigurieren, dass die Standardeinstellungen ignoriert werden. Wenn Sie bestimmte Dateien ignorieren möchten, fügen Sie der Definition eines Elements in der *CSPROJ*-Datei das `Watch="false"`-Attribut hinzu:

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

## <a name="custom-watch-projects"></a>Benutzerdefinierte Überwachungsprojekte

`dotnet-watch` ist nicht auf C#-Projekte beschränkt. Sie können für unterschiedliche Szenarios benutzerdefinierte Überwachungsprojekte erstellen. Betrachten Sie das folgende Projektlayout:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Wenn beide Projekte überwacht werden sollen, erstellen Sie eine benutzerdefinierte Projektdatei, die so konfiguriert ist, dass beide Projekte überwacht werden:

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

Wechseln Sie zum Starten der Dateiüberwachung in beiden Projekten zum *test*-Ordner. Führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest wird ausgeführt, wenn in einer beliebigen Datei in einem der Testprojekte Änderungen vorgenommen werden.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` in GitHub

`dotnet-watch` ist Teil des GitHub-[Aspnet/AspNetCore-Repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
