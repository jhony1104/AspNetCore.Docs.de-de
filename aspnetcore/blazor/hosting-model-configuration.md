---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: Hier erhalten Sie Informationen zur Blazor-Hostingmodellkonfiguration, einschließlich der Integration von Razor-Komponenten in Razor Pages- und MVC-Apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: bd44643877e45c5b48b0972bcc2f637fbc5d98f2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646789"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="788a5-103">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="788a5-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="788a5-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="788a5-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="788a5-105">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="788a5-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="788a5-106">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="788a5-106">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="788a5-107">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="788a5-107">Reflect the connection state in the UI</span></span>

<span data-ttu-id="788a5-108">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="788a5-108">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="788a5-109">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="788a5-109">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="788a5-110">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der Razor-Seite *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="788a5-110">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="788a5-111">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="788a5-111">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="788a5-112">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="788a5-112">CSS class</span></span>                       | <span data-ttu-id="788a5-113">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="788a5-113">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="788a5-114">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="788a5-114">A lost connection.</span></span> <span data-ttu-id="788a5-115">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="788a5-115">The client is attempting to reconnect.</span></span> <span data-ttu-id="788a5-116">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="788a5-116">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="788a5-117">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="788a5-117">An active connection is re-established to the server.</span></span> <span data-ttu-id="788a5-118">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="788a5-118">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="788a5-119">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="788a5-119">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="788a5-120">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="788a5-120">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="788a5-121">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="788a5-121">Reconnection rejected.</span></span> <span data-ttu-id="788a5-122">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="788a5-122">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="788a5-123">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="788a5-123">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="788a5-124">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="788a5-124">This connection state may result when:</span></span><ul><li><span data-ttu-id="788a5-125">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="788a5-125">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="788a5-126">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="788a5-126">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="788a5-127">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="788a5-127">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="788a5-128">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="788a5-128">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="788a5-129">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="788a5-129">Render mode</span></span>

<span data-ttu-id="788a5-130">Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="788a5-130">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="788a5-131">Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="788a5-131">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="788a5-132">`RenderMode` konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="788a5-132">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="788a5-133">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="788a5-133">Is prerendered into the page.</span></span>
* <span data-ttu-id="788a5-134">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält</span><span class="sxs-lookup"><span data-stu-id="788a5-134">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="788a5-135">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="788a5-135">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="788a5-136">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="788a5-136">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="788a5-137">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="788a5-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="788a5-138">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="788a5-138">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="788a5-139">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="788a5-139">Output from the component isn't included.</span></span> <span data-ttu-id="788a5-140">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="788a5-140">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="788a5-141">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="788a5-141">Renders the component into static HTML.</span></span> |

<span data-ttu-id="788a5-142">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="788a5-142">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="788a5-143">Rendern von zustandsbehafteten interaktiven Komponenten von Razor-Seiten und -Ansichten</span><span class="sxs-lookup"><span data-stu-id="788a5-143">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="788a5-144">Zustandsbehaftete interaktive Komponenten können einer Razor-Seite oder -Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="788a5-144">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="788a5-145">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="788a5-145">When the page or view renders:</span></span>

* <span data-ttu-id="788a5-146">Die Komponente wird mit der Seite oder Ansicht vorab gerendert.</span><span class="sxs-lookup"><span data-stu-id="788a5-146">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="788a5-147">Der anfängliche Komponentenzustand, der zum Rendern vorab genutzt wurde, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="788a5-147">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="788a5-148">Der neue Komponentenzustand wird erstellt, wenn die SignalR-Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="788a5-148">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="788a5-149">Die folgende Razor-Seite rendert eine `Counter`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="788a5-149">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="788a5-150">Rendern von nicht interaktiven Komponenten von Razor-Seiten und Ansichten</span><span class="sxs-lookup"><span data-stu-id="788a5-150">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="788a5-151">Auf der folgenden Razor-Seite wird die `Counter`-Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="788a5-151">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="788a5-152">Da `MyComponent` statisch gerendert wird, darf die Komponente nicht interaktiv sein.</span><span class="sxs-lookup"><span data-stu-id="788a5-152">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="788a5-153">Konfigurieren des SignalR-Clients für Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="788a5-153">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="788a5-154">In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="788a5-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="788a5-155">Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="788a5-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="788a5-156">So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="788a5-156">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="788a5-157">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="788a5-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="788a5-158">Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.</span><span class="sxs-lookup"><span data-stu-id="788a5-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
