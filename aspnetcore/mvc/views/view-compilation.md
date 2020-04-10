---
title: Kompilieren einer Razor-Datei in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor-Dateien in einer ASP.NET Core-App auftreten kann.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994605"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Kompilieren einer Razor-Datei in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor-Dateien mit einer *.cshtml*-Erweiterung werden mit dem [Razor SDK](xref:razor-pages/sdk) zur Erstellung und zur Veröffentlichung kompiliert. Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.

## <a name="razor-compilation"></a>Razor-Kompilierung

Die Kompilierung zur Erstellung und Veröffentlichung von Razor-Dateien wird standardmäßig vom Razor SDK aktiviert. Wenn die Laufzeitkompilierung aktiviert ist, ergänzt diese die Kompilierung zur Buildzeit, wodurch Razor-Dateien aktualisiert werden können, wenn sie bearbeitet werden.

## <a name="runtime-compilation"></a>Laufzeitkompilierung

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

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