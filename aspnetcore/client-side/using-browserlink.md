---
title: Browserlink in ASP.NET Core
author: ncarandini
description: Erläutert, wie Browser Link für ein Visual Studio-Feature ist, die die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpft.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894707"
---
# <a name="browser-link-in-aspnet-core"></a>Browserlink in ASP.NET Core

Durch [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), und [Tom Dykstra](https://github.com/tdykstra)

Browserverknüpfung ist ein Feature in Visual Studio, der einen Kommunikationskanal zwischen der Entwicklungs- und eine oder mehrere Webbrowser erstellt. Sie können Browser Link, um Ihre Webanwendung in mehreren Browsern gleichzeitig zu aktualisieren, dies ist hilfreich für browserübergreifende Tests verwenden.

## <a name="browser-link-setup"></a>Setup für Browser Link

::: moniker range=">= aspnetcore-2.1"

Beim Konvertieren von einem ASP.NET Core 2.0-Projekt in ASP.NET Core 2.1 und auf der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), installieren Sie die [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) für Paket BrowserLink-Funktionalität. Verwenden Sie die Projektvorlagen für ASP.NET Core 2.1 die `Microsoft.AspNetCore.App` metapaket standardmäßig.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Webanwendung**, **leere**, und **Web-API-** Projekt Vorlagen verwenden die [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) , die für die Paketverweise enthält [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Daher wird die Verwendung der `Microsoft.AspNetCore.All` metapaket erfordert keine weitere Aktion aus, um Browser Link für die Verwendung verfügbar zu machen.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Die ASP.NET Core 1.x **Webanwendung** -Projektvorlage verfügt über einen Paketverweis für die [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Paket. Die **leere** oder **Web-API-** -Vorlagenprojekten müssen Sie zum Hinzufügen von paketverweisen zu `Microsoft.VisualStudio.Web.BrowserLink`.

Da dies eine Visual Studio-Funktion, die einfachste Möglichkeit, fügen das Paket ist eine **leere** oder **Web-API-** -Vorlagenprojekt besteht darin, öffnen die **-Paket-Manager-Konsole** (**Ansicht** > **Other Windows** > **-Paket-Manager-Konsole**), und führen Sie den folgenden Befehl aus:

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Alternativ können Sie **NuGet Package Manager**. Mit der rechten Maustaste in des Namens des Projekts **Projektmappen-Explorer** , und wählen Sie **NuGet-Pakete verwalten**:

![Öffnen von NuGet-Paket-Manager](using-browserlink/_static/open-nuget-package-manager.png)

Suchen Sie und installieren Sie das Paket:

![Paket mit NuGet-Paket-Manager hinzufügen](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>Konfiguration

Für die `Startup.Configure`-Methode gilt Folgendes:

```csharp
app.UseBrowserLink();
```

Der Code in der Regel befindet sich in einem `if` Block, der Browser Link in der Entwicklungsumgebung, nur wie hier gezeigt kann:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Gewusst wie: Verwenden von Browser Link

Wenn Sie eine ASP.NET Core-Projekt geöffnet haben, zeigt Visual Studio das Symbolleisten-Steuerelement von Browser Link neben der **Debugziel** Toolbar-Steuerelement:

![Browser Link Dropdown-Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

Auf dem Browserlink-Symbolleisten-Steuerelement können Sie folgende Aktionen ausführen:

* Aktualisieren Sie die Webanwendung in mehreren Browsern gleichzeitig.
* Öffnen der **Browserlink-Dashboard**.
* Aktivieren oder deaktivieren Sie **Browserlink**. Hinweis: Browserverknüpfung ist in Visual Studio 2017 (Version 15.3) standardmäßig deaktiviert.
* Aktivieren oder deaktivieren Sie [automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Einige Visual Studio-Plug-ins, insbesondere *2015 für Web-Erweiterung Pack* und *Web Erweiterung Pack 2017*, bieten Sie erweiterte Funktionen für Browser Link, aber einige der zusätzlichen Features funktionieren nicht mit ASP. NET-Core-Projekte.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aktualisieren Sie die Web-app in mehreren Browsern gleichzeitig

Um einen einzelnen Webbrowser zum Starten, wenn das Projekt starten zu auszuwählen, verwenden Sie im Dropdown-Menü in der **Debugziel** Toolbar-Steuerelement:

![F5-Dropdownmenü](using-browserlink/_static/debug-target-dropdown-menu.png)

Wählen Sie zum Öffnen von mehreren Browsern gleichzeitig **Browserauswahl...**  aus der gleichen Dropdownliste aus. Halten Sie die STRG-Taste auf die Browser auswählen, werden soll, und klicken Sie dann auf **Durchsuchen**:

![Öffnen Sie die vielen Browsern gleichzeitig](using-browserlink/_static/open-many-browsers-at-once.png)

Hier ist ein Screenshot der Visual Studio mit der Ansicht "Index" öffnen und zwei geöffneten Browser:

![Synchronisieren Sie mit zwei Browser-Beispiel](using-browserlink/_static/sync-with-two-browsers-example.png)

Zeigen Sie auf dem Browserlink-Symbolleisten-Steuerelement auf den Browsern, die dem Projekt verbunden sind, finden Sie unter:

![Zeigen Sie mit tip](using-browserlink/_static/hoover-tip.png)

Ändern die Ansicht "Index", und aller verbundenen Browser werden aktualisiert, wenn Sie den Browserlink-Schaltfläche "Aktualisieren" klicken:

![Browser-Sync-zu-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browserverknüpfung funktioniert auch mit Browsern, die Sie außerhalb von Visual Studio starten, und navigieren Sie zu der Anwendungs-URL.

### <a name="the-browser-link-dashboard"></a>Die Browserlink-Dashboard

Öffnen Sie den Browserlink-Dashboard, aus der Browserlink-Dropdown-Menü, um die Verbindung mit geöffneten Browser zu verwalten:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Wenn kein Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, durch Auswählen der *in Browser anzeigen* Link:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Andernfalls werden die verbundenen Browsern durch den Pfad zu der Seite angezeigt, die von jedem Browser angezeigt wird:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Wenn Sie möchten, können Sie einen Namen aufgeführten Browser diese einzelnen Browser aktualisieren, klicken.

### <a name="enable-or-disable-browser-link"></a>Aktivieren oder Deaktivieren von Browser Link

Wenn Sie Browser Link erneut aktivieren nach dem deaktivieren, müssen Sie den Browser, um sie erneut eine Verbindung herstellen aktualisieren.

### <a name="enable-or-disable-css-auto-sync"></a>Aktivieren Sie oder deaktivieren Sie die automatische CSS-Synchronisierung

Wenn die automatische CSS-Synchronisierung aktiviert ist, werden die verbundene Browsern automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.

## <a name="how-it-works"></a>So funktioniert es

Browserverknüpfung verwendet SignalR, um einen Kommunikationskanal zwischen Visual Studio und den Browser zu erstellen. Wenn Browser Link aktiviert ist, fungiert Visual Studio als SignalR-Server, dem mehrere Clients (Browser) herstellen können. Browserverknüpfung registriert auch eine middlewarekomponente in der ASP.NET Core-Anforderungspipeline. Diese Komponente fügt spezielle `<script>` verweisen in jede Anforderung einer Seite vom Server. Sie sehen die Skriptverweise dazu **Quelltext anzeigen** im Browser, und scrollen bis zum Ende der `<body>` Inhalte:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Die Quelldateien werden nicht geändert werden. Die middlewarekomponente fügt die Skriptverweise dynamisch.

Da der Browser-Side-Code alle JavaScript ist, funktioniert in allen Browsern, die SignalR unterstützt werden, ohne dass einen Browser-Plug-in.
