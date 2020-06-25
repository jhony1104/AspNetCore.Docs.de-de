---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erhalten Sie Informationen zu zusätzlichen Szenarios für die Blazor-Hostingmodellkonfiguration in ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 726aafd2bf5d3469c30ebce1e4eea8ed8ec8d58e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103261"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="30acc-103">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="30acc-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="30acc-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="30acc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="30acc-105">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="30acc-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="30acc-106">Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="30acc-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="30acc-107">*Dieser Abschnitt gilt für Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="30acc-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="30acc-108">So konfigurieren Sie den zugrunde liegenden SignalR-Client zum Senden von Anmeldeinformationen (z. B. Cookies oder HTTP-Authentifizierungsheader):</span><span class="sxs-lookup"><span data-stu-id="30acc-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="30acc-109">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Methode, um die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>-Enumeration auf ursprungsübergreifende [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)-Anforderungen festzulegen:</span><span class="sxs-lookup"><span data-stu-id="30acc-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="30acc-110">Weisen Sie der <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>-Eigenschaft die <xref:System.Net.Http.HttpMessageHandler>-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="30acc-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="30acc-111">Weitere Informationen finden Sie unter <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="30acc-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="30acc-112">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="30acc-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="30acc-113">*Dieser Abschnitt gilt für Blazor-Server.*</span><span class="sxs-lookup"><span data-stu-id="30acc-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="30acc-114">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="30acc-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="30acc-115">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="30acc-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="30acc-116">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der *_Host.cshtml*-Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="30acc-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="30acc-117">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="30acc-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="30acc-118">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="30acc-118">CSS class</span></span>                       | <span data-ttu-id="30acc-119">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="30acc-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="30acc-120">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="30acc-120">A lost connection.</span></span> <span data-ttu-id="30acc-121">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="30acc-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="30acc-122">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="30acc-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="30acc-123">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="30acc-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="30acc-124">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="30acc-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="30acc-125">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="30acc-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="30acc-126">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="30acc-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="30acc-127">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="30acc-127">Reconnection rejected.</span></span> <span data-ttu-id="30acc-128">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="30acc-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="30acc-129">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="30acc-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="30acc-130">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="30acc-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="30acc-131">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="30acc-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="30acc-132">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="30acc-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="30acc-133">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="30acc-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="30acc-134">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="30acc-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="30acc-135">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="30acc-135">Render mode</span></span>

<span data-ttu-id="30acc-136">*Dieser Abschnitt gilt für Blazor-Server.*</span><span class="sxs-lookup"><span data-stu-id="30acc-136">*This section applies to Blazor Server.*</span></span>

Blazor<span data-ttu-id="30acc-137"> Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="30acc-137"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="30acc-138">Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="30acc-138">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="30acc-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="30acc-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="30acc-140">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="30acc-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="30acc-141">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="30acc-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="30acc-142">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="30acc-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="30acc-143">Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="30acc-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="30acc-144">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="30acc-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="30acc-145">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="30acc-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="30acc-146">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="30acc-146">Output from the component isn't included.</span></span> <span data-ttu-id="30acc-147">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="30acc-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="30acc-148">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="30acc-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="30acc-149">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="30acc-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="30acc-150">Konfigurieren des SignalR-Clients für Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="30acc-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="30acc-151">*Dieser Abschnitt gilt für Blazor-Server.*</span><span class="sxs-lookup"><span data-stu-id="30acc-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="30acc-152">In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="30acc-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="30acc-153">Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="30acc-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="30acc-154">So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="30acc-154">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="30acc-155">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="30acc-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="30acc-156">Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.</span><span class="sxs-lookup"><span data-stu-id="30acc-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="30acc-157">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="30acc-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
