---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277267"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilieren einer Razor-Datei in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert. Die Laufzeitkompilierung kann optional durch Konfigurieren des Projekts aktiviert werden.

## <a name="razor-compilation"></a>Razor-Kompilierung

Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert. Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.

## <a name="enable-runtime-compilation-at-project-creation"></a>Aktivieren der Laufzeitkompilierung bei der Projekterstellung

Die Vorlagen für Razor Pages und MVC-Projekt enthalten eine Option zum Aktivieren der Laufzeitkompilierung beim Erstellen des Projekts. Diese Option wird in ASP.NET Core 3.1 und höher unterstützt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Im **Dialogfeld Erstellen eines neuen ASP.NET Core-Webanwendungsdialogfeld:**

1. Wählen Sie entweder die **Projektvorlage Webanwendung** oder **Webanwendung (Model-View-Controller)** aus.
1. Aktivieren Sie das Kontrollkästchen **Razor-Laufzeitkompilierung aktivieren.**

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Verwenden `-rrc` Sie `--razor-runtime-compilation` die Option oder Vorlage. Der folgende Befehl erstellt beispielsweise ein neues Razor Pages-Projekt mit aktivierter Laufzeitkompilierung:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt

So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen in einem vorhandenen Projekt:

1. das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.
1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält. Beispiel:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Bedingtes Aktivieren der Laufzeitkompilierung in einem vorhandenen Projekt

Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist. Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:

* Es werden kompilierte Ansichten verwendet.
* Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.

So aktivieren Sie die Laufzeitkompilierung nur in der Entwicklungsumgebung:

1. das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.
1. Ändern Sie `environmentVariables` den Abschnitt "Startprofil" in *launchSettings.json*:
    * Verify `ASPNETCORE_ENVIRONMENT` ist `"Development"`auf festgelegt.
    * Legen Sie `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` auf `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` fest.

Im folgenden Beispiel ist die Laufzeitkompilierung in `IIS Express` `RazorPagesApp` der Entwicklungsumgebung für die und Startprofile aktiviert:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

In der `Startup` Projektklasse sind keine Codeänderungen erforderlich. Zur Laufzeit sucht ASP.NET Core nach einem [HostingStartup-Attribut](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) auf Assemblyebene in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. Das `HostingStartup` Attribut gibt den auszuführenden App-Startcode an. Dieser Startcode ermöglicht die Laufzeitkompilierung.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert. Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.

## <a name="razor-compilation"></a>Razor-Kompilierung

Die Erstellung von Razor-Dateien zur Erstellung von Razor-Dateien ist standardmäßig durch das Razor SDK aktiviert. Wenn diese Option aktiviert ist, ergänzt die Laufzeitkompilierung die Erstellung von Buildzeit, sodass Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.

## <a name="runtime-compilation"></a>Laufzeitkompilierung

So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:

1. das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) installieren.

1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält. Beispiel:

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

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [RazorCompileOnBuild- und RazorCompileOnPublish-Eigenschaften.](xref:razor-pages/sdk#properties)
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Im Beispiel für die [Laufzeitkompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, dass die Laufzeitkompilierung projektübergreifend funktioniert.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine Razor-Datei wird zur Laufzeit kompiliert, wenn die zugeordnete Razor-Seite oder MVC-Ansicht aufgerufen wird. Razor-Dateien werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert.

## <a name="razor-compilation"></a>Razor-Kompilierung

Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert. Das Bearbeiten von Razor-Dateien, nachdem sie aktualisiert wurden, wird zum Zeitpunkt der Erstellung unterstützt. Standardmäßig werden nur die kompilierten *Views.dll*- und keine *.cshtml*-Dateien oder -Verweise auf Assemblys, die für die Kompilierung von Razor-Dateien benötigt werden, in Ihrer App bereitgestellt.

> [!IMPORTANT]
> Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt. Wir empfehlen die Migration zu [Razor Sdk](xref:razor-pages/sdk).
>
> Das Razor SDK ist nur dann wirksam, wenn keine für die Vorkompilierung spezifischen Eigenschaften in der Projektdatei festgelegt sind. Durch Festlegen der Eigenschaft `MvcRazorCompileOnPublish` der *CSPROJ*-Datei auf `true` wird das Razor SDK beispielsweise deaktiviert.

## <a name="runtime-compilation"></a>Laufzeitkompilierung

Die Kompilierung zur Buildzeit wird durch die Laufzeitkompilierung von Razor-Dateien ergänzt. Der ASP.NET Core-MVC kompiliert Razor-Dateien erneut, wenn sich die Inhalte einer *.cshtml*-Datei ändern.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
