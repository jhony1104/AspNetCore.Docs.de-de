---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0a5770a00c5cb319b571628659a07e73e0de54f9
ms.sourcegitcommit: fd2483f0a384b1c479c5b4af025ee46917db1919
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74867977"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilieren einer Razor-Datei in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete MVC-Ansicht aufgerufen wird. Die Veröffentlichung von Razor-Dateien zum Zeitpunkt der Erstellung wird nicht unterstützt. Razor-Dateien können optional zur Veröffentlichung kompiliert und mit der App über das Vorkompilierungstool bereitgestellt werden.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird. Die Veröffentlichung von Razor-Dateien zum Zeitpunkt der Erstellung wird nicht unterstützt. Razor-Dateien können optional zur Veröffentlichung kompiliert und mit der App über das Vorkompilierungstool bereitgestellt werden.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird. Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert. Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.

::: moniker-end

## <a name="razor-compilation"></a>Razor-Kompilierung

::: moniker range=">= aspnetcore-3.0"

Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert. Wenn die Laufzeitkompilierung aktiviert ist, ergänzt diese die Kompilierung zur Buildzeit, wodurch Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert. Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt. Standardmäßig werden nur die kompilierten *Views.dll*- und keine *.cshtml*-Dateien oder -Verweise auf Assemblys, die für die Kompilierung von Razor-Dateien benötigt werden, in Ihrer App bereitgestellt.

> [!IMPORTANT]
> Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt. Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).
>
> Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind. Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Wenn Ihr Projekt .NET Framework als Ziel verwendet, installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Wenn Ihr Paket für .NET Core gedacht ist, sind keine Änderungen erforderlich.

Mit den Projektvorlagen von ASP.NET Core 2.x wird die Eigenschaft `MvcRazorCompileOnPublish` standardmäßig explizit auf `true` festgelegt. Dieses Element kann folglich sicher aus der *CSPROJ*-Datei entfernt werden.

> [!IMPORTANT]
> Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt. Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).
>
> Die Vorkompilierung von Razor-Dateien steht aktuell beim Durchführen einer [eigenständigen Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0 nicht zur Verfügung.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Legen Sie die Eigenschaft `MvcRazorCompileOnPublish` auf `true` fest und installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/). Das folgende *CSPROJ*-Beispiel veranschaulicht diese Einstellungen:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Bereiten Sie die App mit dem [Veröffentlichungsbefehl der .NET Core-CLI](/dotnet/core/tools/dotnet-publish) für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) vor. Führen Sie z. B. den folgenden Befehl auf der Stammebene des Projekts aus:

```dotnetcli
dotnet publish -c Release
```

Die Datei *\<project_name>.PrecompiledViews.dll*, welche die kompilierten Razor-Dateien enthält, wird erzeugt, wenn die Vorkompilierung erfolgreich abgeschlossen wurde. Im unten stehenden Screenshot wird beispielsweise der Inhalt von *Index.cshtml* in *WebApplication1.PrecompiledViews.dll* dargestellt:

![Razor-Ansichten in einer DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Laufzeitkompilierung

::: moniker range="= aspnetcore-2.1"

Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt. Der ASP.NET Core-MVC kompiliert Razor-Dateien erneut, wenn sich die Inhalte einer *.cshtml*-Datei ändern.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt. <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> übernimmt oder bestimmt einen Wert, der bestimmt, ob Razor-Dateien (Razor-Ansichten und Razor Pages) neu kompiliert und aktualisiert werden, wenn Dateien auf dem Datenträger sich ändern.

Der Standardwert ist `true` für:

* Wenn die Kompatibilitätsversion der App auf <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> oder früher festgelegt wurde.
* Wenn die Kompatibilitätsversion der App auf <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> oder höher festgelegt wurde, und sich die App in der Entwicklungsumgebung <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> befindet. Anders gesagt: Razor-Dateien lassen sich in Nicht-Entwicklungsumgebungen nicht erneut kompilieren, es sei denn, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> ist explizit festgelegt.

Anleitungen und Beispiele zum Festlegen der Kompatibilitätsversion der App finden Sie unter <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:

1. das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.

1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält. Beispiel:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Bedingte Aktivierung der Laufzeitkompilierung

Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist. Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:

* Es werden kompilierte Ansichten verwendet.
* Die Größe ist geringer.
* Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.

So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:

1. Geben Sie einen bedingten Verweis auf das [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)-Paket basierend auf dem aktiven `Configuration`-Wert an:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält. Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:

    ```csharp
    public IWebHostEnvironment Env { get; set; }
    
    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();
    
    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
