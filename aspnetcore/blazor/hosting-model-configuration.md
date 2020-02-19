---
title: Konfiguration des ASP.net Core Blazor Hostingmodells
author: guardrex
description: Erfahren Sie mehr über die Konfiguration von Blazor Hostingmodellen, einschließlich der Integration von Razor-Komponenten in Razor Pages und MVC-apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447034"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="35a8c-103">Konfiguration des ASP.net Core blazor-Hostingmodells</span><span class="sxs-lookup"><span data-stu-id="35a8c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="35a8c-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="35a8c-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="35a8c-105">In diesem Artikel wird die Konfiguration des Hostingmodells behandelt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="35a8c-106">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="35a8c-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="35a8c-107">Reflektieren des Verbindungs Zustands in der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="35a8c-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="35a8c-108">Wenn der Client erkennt, dass die Verbindung unterbrochen wurde, wird dem Benutzer eine Standardbenutzer Oberfläche angezeigt, während der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="35a8c-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="35a8c-109">Wenn bei der erneuten Verbindungs Herstellung ein Fehler auftritt, wird dem Benutzer die Option zum erneuten versuchen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="35a8c-110">Zum Anpassen der Benutzeroberfläche definieren Sie ein Element mit einem `id` `components-reconnect-modal` im `<body>` der Razor Page *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="35a8c-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="35a8c-111">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal` Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="35a8c-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="35a8c-112">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="35a8c-112">CSS class</span></span>                       | <span data-ttu-id="35a8c-113">Gibt&hellip;</span><span class="sxs-lookup"><span data-stu-id="35a8c-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="35a8c-114">Eine verlorene Verbindung.</span><span class="sxs-lookup"><span data-stu-id="35a8c-114">A lost connection.</span></span> <span data-ttu-id="35a8c-115">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="35a8c-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="35a8c-116">Zeigen Sie die modale an.</span><span class="sxs-lookup"><span data-stu-id="35a8c-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="35a8c-117">Eine aktive Verbindung mit dem Server wird wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="35a8c-118">Blenden Sie den modalen aus.</span><span class="sxs-lookup"><span data-stu-id="35a8c-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="35a8c-119">Fehler beim erneuten Herstellen der Verbindung, wahrscheinlich aufgrund eines Netzwerk Fehlers.</span><span class="sxs-lookup"><span data-stu-id="35a8c-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="35a8c-120">Um erneut eine Verbindung herzustellen, wenden Sie `window.Blazor.reconnect()`an.</span><span class="sxs-lookup"><span data-stu-id="35a8c-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="35a8c-121">Erneute Verbindung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-121">Reconnection rejected.</span></span> <span data-ttu-id="35a8c-122">Der Server wurde erreicht, hat jedoch die Verbindung abgelehnt, und der Benutzer Zustand auf dem Server geht verloren.</span><span class="sxs-lookup"><span data-stu-id="35a8c-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="35a8c-123">Um die APP erneut zu laden, wenden Sie `location.reload()`an.</span><span class="sxs-lookup"><span data-stu-id="35a8c-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="35a8c-124">Der Verbindungsstatus kann folgende Ergebnisse haben:</span><span class="sxs-lookup"><span data-stu-id="35a8c-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="35a8c-125">Ein Absturz in der serverseitigen Verbindung tritt auf.</span><span class="sxs-lookup"><span data-stu-id="35a8c-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="35a8c-126">Der Client ist so lange getrennt, dass der Server den Zustand des Benutzers löscht.</span><span class="sxs-lookup"><span data-stu-id="35a8c-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="35a8c-127">Instanzen der Komponenten, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="35a8c-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="35a8c-128">Der Server wird neu gestartet, oder der Arbeitsprozess der APP wird wieder verwendet.</span><span class="sxs-lookup"><span data-stu-id="35a8c-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="35a8c-129">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="35a8c-129">Render mode</span></span>

<span data-ttu-id="35a8c-130">Blazor-Server-apps werden standardmäßig so eingerichtet, dass Sie die Benutzeroberfläche auf dem Server vorab ausführen, bevor die Client Verbindung mit dem Server hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="35a8c-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="35a8c-131">Dies wird auf der Razor Page *_Host. cshtml* eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="35a8c-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="35a8c-132">`RenderMode` konfiguriert, ob die Komponente Folgendes hat:</span><span class="sxs-lookup"><span data-stu-id="35a8c-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="35a8c-133">Wird in die Seite vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="35a8c-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="35a8c-134">Wird als statischer HTML-Code auf der Seite gerendert, oder wenn er die erforderlichen Informationen zum Bootstrapping einer blazor-APP vom Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="35a8c-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="35a8c-135">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="35a8c-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="35a8c-136">Rendert die Komponente in statischem HTML-Format und enthält einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="35a8c-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="35a8c-137">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="35a8c-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="35a8c-138">Rendert einen Marker für eine Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="35a8c-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="35a8c-139">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="35a8c-139">Output from the component isn't included.</span></span> <span data-ttu-id="35a8c-140">Wenn der Benutzer-Agent gestartet wird, wird dieser Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="35a8c-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="35a8c-141">Rendert die Komponente in statischem HTML-Format.</span><span class="sxs-lookup"><span data-stu-id="35a8c-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="35a8c-142">Das Rendering von Serverkomponenten von einer statischen HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="35a8c-143">Zustands behaftete interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="35a8c-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="35a8c-144">Zustands behaftete interaktive Komponenten können einer Razor Page oder Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="35a8c-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="35a8c-145">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="35a8c-145">When the page or view renders:</span></span>

* <span data-ttu-id="35a8c-146">Die Komponente wird mit der Seite oder der Ansicht vorab überstehen.</span><span class="sxs-lookup"><span data-stu-id="35a8c-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="35a8c-147">Der anfängliche Komponenten Zustand, der für die vorab Generierung verwendet wird, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="35a8c-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="35a8c-148">Der neue Komponenten Status wird erstellt, wenn die SignalR Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="35a8c-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="35a8c-149">Die folgende Razor Page rendert eine `Counter` Komponente:</span><span class="sxs-lookup"><span data-stu-id="35a8c-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="35a8c-150">Nicht interaktive Komponenten von Razor Pages und Ansichten</span><span class="sxs-lookup"><span data-stu-id="35a8c-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="35a8c-151">Auf der folgenden Razor-Seite wird die `Counter` Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="35a8c-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="35a8c-152">Da `MyComponent` statisch gerendert wird, kann die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="35a8c-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="35a8c-153">Konfigurieren des SignalR Clients für Blazor Server-apps</span><span class="sxs-lookup"><span data-stu-id="35a8c-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="35a8c-154">Manchmal müssen Sie den SignalR Client konfigurieren, der von Blazor Server-Apps verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="35a8c-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="35a8c-155">Beispielsweise können Sie die Protokollierung für den SignalR Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="35a8c-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="35a8c-156">So konfigurieren Sie den SignalR-Client in der Datei *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="35a8c-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="35a8c-157">Fügen Sie dem `<script>`-Tag für das `blazor.server.js` Skript ein `autostart="false"` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="35a8c-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="35a8c-158">Ruft `Blazor.start` auf und übergibt ein Konfigurationsobjekt, das den SignalR Generator angibt.</span><span class="sxs-lookup"><span data-stu-id="35a8c-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
