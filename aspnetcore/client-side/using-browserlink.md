---
title: Browser Link in ASP.net Core
author: ncarandini
description: Erläutert, wie Browser Link eine Visual Studio-Funktion ist, die die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpft.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828269"
---
# <a name="browser-link-in-aspnet-core"></a>Browser Link in ASP.net Core

Von [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)und [Tom Dykstra](https://github.com/tdykstra)

Browser Link ist ein Visual Studio-Feature. Er erstellt einen Kommunikationskanal zwischen der Entwicklungsumgebung und einem oder mehreren Webbrowsern. Sie können die Browser Verknüpfung verwenden, um Ihre Web-App in mehreren Browsern gleichzeitig zu aktualisieren, was für Browser übergreifende Tests nützlich ist.

## <a name="browser-link-setup"></a>Browser Link einrichten

::: moniker range=">= aspnetcore-3.0"

Fügen Sie dem Projekt das [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) -Paket hinzu. Aktivieren Sie für ASP.net Core Razor Pages-oder MVC-Projekte auch die Lauf Zeit Kompilierung von Razor-Dateien ( *. cshtml*), wie unter <xref:mvc/views/view-compilation>beschrieben. Razor-Syntax Änderungen werden nur angewendet, wenn die Laufzeit-Kompilierung aktiviert wurde.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Wenn Sie ein ASP.net Core 2,0-Projekt in ASP.net Core 2,1 und den Übergang in das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" umrechnen, installieren Sie das Paket " [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) " für Browser Link Funktionalität. In den ASP.net Core 2,1-Projektvorlagen wird standardmäßig das `Microsoft.AspNetCore.App` Metapaket verwendet.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

In den Projektvorlagen für die ASP.net Core 2,0- **Webanwendung**, **leere**und **Web-API** wird das [Microsoft. aspnetcore. all-Metapaket](xref:fundamentals/metapackage)verwendet, das einen Paket Verweis für [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)enthält. Daher ist für die Verwendung des `Microsoft.AspNetCore.All` Metapakets keine weitere Aktion erforderlich, um den Browser Link zur Verwendung verfügbar zu machen.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Die Projektvorlage "ASP.net Core 1. x- **Webanwendung** " enthält einen Paket Verweis für das Paket " [Microsoft. VisualStudio. Web. browserlink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) ". Andere Projekttypen erfordern, dass Sie `Microsoft.VisualStudio.Web.BrowserLink`einen Paket Verweis hinzufügen.

::: moniker-end

### <a name="configuration"></a>-Konfiguration

Rufen Sie `UseBrowserLink` in der `Startup.Configure`-Methode auf:

```csharp
app.UseBrowserLink();
```

Der `UseBrowserLink`-Befehl wird in der Regel in einem `if`-Block platziert, der nur den Browser Link in der Entwicklungsumgebung aktiviert. Beispiel:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Verwenden von Browser Link

Wenn Sie ein ASP.net Core Projekt geöffnet haben, zeigt Visual Studio das Browser Link Symbolleisten-Steuerelement neben dem Symbolleisten-Steuerelement **Debug-Ziel** an:

![Browser Link-Dropdown Menü](using-browserlink/_static/browserLink-dropdown-menu.png)

Im Browser Link Symbolleisten-Steuerelement können Sie folgende Aktionen ausführen:

* Aktualisieren Sie die Web-App in mehreren Browsern gleichzeitig.
* Öffnen Sie das **Browser Link-Dashboard**.
* Aktiviert oder deaktiviert den **Browser Link**. Hinweis: der Browser Link ist in Visual Studio standardmäßig deaktiviert.
* Aktivieren oder deaktivieren Sie die [Automatische CSS-Synchronisierung](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Die Web-App in mehreren Browsern gleichzeitig aktualisieren

Verwenden Sie zum Auswählen eines einzelnen Webbrowsers, der beim Starten des Projekts gestartet werden soll, das Dropdown Menü im Symbolleisten-Steuerelement **Debug-Ziel** :

![F5-Dropdown Menü](using-browserlink/_static/debug-target-dropdown-menu.png)

Wenn Sie mehrere Browser gleichzeitig öffnen möchten, wählen Sie in der Dropdown Liste die Option **Durchsuchen mit...** aus. Halten Sie die <kbd>STRG</kbd> -Taste gedrückt, um die gewünschten Browser auszuwählen, und klicken Sie dann auf **Durchsuchen**:

![Viele Browser gleichzeitig öffnen](using-browserlink/_static/open-many-browsers-at-once.png)

Der folgende Screenshot zeigt Visual Studio mit der geöffneten Index Ansicht und zwei geöffneten Browsern:

![Beispiel für Synchronisierung mit zwei Browsern](using-browserlink/_static/sync-with-two-browsers-example.png)

Zeigen Sie auf das Symbolleisten-Steuerelement Browser Link, um die Browser anzuzeigen, die mit dem Projekt verbunden sind:

![Hover-Tipp](using-browserlink/_static/hoover-tip.png)

Ändern Sie die Index Sicht, und alle verbundenen Browser werden aktualisiert, wenn Sie auf die Schaltfläche zum Aktualisieren des Browser Links klicken:

![Browser-Sync-zu-Änderungen](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link funktioniert auch mit Browsern, die Sie von außerhalb von Visual Studio starten, und navigieren Sie zur App-URL.

### <a name="the-browser-link-dashboard"></a>Das Browser Link-Dashboard

Öffnen Sie im Dropdown Menü Browser Link das Fenster **Browser Link-Dashboard** , um die Verbindung mit geöffneten Browsern zu verwalten:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Wenn kein Browser verbunden ist, können Sie eine nicht-Debugsitzung starten, indem Sie den Link **in Browser anzeigen** auswählen:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Andernfalls werden die verbundenen Browser mit dem Pfad zu der Seite angezeigt, die von den einzelnen Browsern angezeigt wird:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Sie können auch auf einen einzelnen Browser Namen klicken, um nur diesen Browser zu aktualisieren.

### <a name="enable-or-disable-browser-link"></a>Browser Link aktivieren oder deaktivieren

Wenn Sie den Browser Link nach der Deaktivierung erneut aktivieren, müssen Sie die Browser aktualisieren, um Sie erneut zu verbinden.

### <a name="enable-or-disable-css-auto-sync"></a>Aktivieren oder Deaktivieren der automatischen CSS-Synchronisierung

Wenn die automatische CSS-Synchronisierung aktiviert ist, werden verbundene Browser automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.

## <a name="how-it-works"></a>So funktioniert's

Browser Link verwendet [SignalR](xref:signalr/introduction) , um einen Kommunikationskanal zwischen Visual Studio und dem Browser zu erstellen. Wenn der Browser Link aktiviert ist, fungiert Visual Studio als SignalR Server, mit dem mehrere Clients (Browser) eine Verbindung herstellen können. Der Browser Link registriert auch eine Middlewarekomponente in der ASP.net Core Anforderungs Pipeline. Diese Komponente fügt besondere `<script>` Verweise auf jede Seiten Anforderung vom Server ein. Sie können die Skript Verweise anzeigen, indem Sie im Browser **Quelle anzeigen** auswählen und einen Bildlauf zum Ende des `<body>`-Taginhalts durchführen:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Die Quelldateien werden nicht geändert. Die Middleware-Komponente fügt die Skript Verweise dynamisch ein.

Da es sich bei dem Browser seitigen Code um JavaScript handelt, funktioniert er für alle Browser, die SignalR unterstützt, ohne dass ein Browser-Plug-in erforderlich ist.
