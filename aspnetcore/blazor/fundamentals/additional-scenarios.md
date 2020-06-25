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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Hostingmodellkonfiguration für ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Dieser Artikel behandelt die Hostingmodellkonfiguration.

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung

*Dieser Abschnitt gilt für Blazor WebAssembly.*

So konfigurieren Sie den zugrunde liegenden SignalR-Client zum Senden von Anmeldeinformationen (z. B. Cookies oder HTTP-Authentifizierungsheader):

* Verwenden Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>-Methode, um die <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>-Enumeration auf ursprungsübergreifende [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)-Anforderungen festzulegen:

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

* Weisen Sie der <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>-Eigenschaft die <xref:System.Net.Http.HttpMessageHandler>-Klasse hinzu:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Weitere Informationen finden Sie unter <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Reflektieren des Verbindungszustands auf der Benutzeroberfläche

*Dieser Abschnitt gilt für Blazor-Server.*

Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen. Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.

Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` der *_Host.cshtml*-Razor-Seite:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.

| CSS-Klasse                       | Steht für&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Die Verbindung wurde getrennt. Der Client versucht, die Verbindung wiederherzustellen. Die modale Seite wird angezeigt. |
| `components-reconnect-hide`     | Auf dem Server wird eine aktive Verbindung wiederhergestellt. Die modale Seite wird ausgeblendet. |
| `components-reconnect-failed`   | Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen. Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen. |
| `components-reconnect-rejected` | Die Wiederherstellung der Verbindung wurde abgelehnt. Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen. Rufen Sie `location.reload()` auf, um die App neu zu laden. Dieser Verbindungszustand kann aus folgenden Gründen auftreten:<ul><li>Aufgetretener Absturz auf der serverseitigen Verbindung.</li><li>Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat. Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</li><li>Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</li></ul> |

## <a name="render-mode"></a>Rendermodus

*Dieser Abschnitt gilt für Blazor-Server.*

Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern. Sehen Sie sich die Einrichtung der Razor-Seite *_Host.cshtml* an:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Beschreibung |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die Komponente in statische HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die Komponente in statischen HTML-Code. |

Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Konfigurieren des SignalR-Clients für Blazor Server-Apps

*Dieser Abschnitt gilt für Blazor-Server.*

In einigen Fällen müssen Sie den von den SignalR Server-Apps verwendeten Blazor-Client konfigurieren. Beispielsweise können Sie die Protokollierung auf dem SignalR-Client konfigurieren, um ein Verbindungsproblem zu diagnostizieren.

So konfigurieren Sie den SignalR-Client in der Datei *Pages/_Host.cshtml*:

* Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.
* Rufen Sie `Blazor.start` auf, und übergeben ein Konfigurationsobjekt, das den SignalR-Builder angibt.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
