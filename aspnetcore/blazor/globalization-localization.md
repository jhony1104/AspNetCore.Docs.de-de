---
title: Globalisierung und Lokalisierung in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie Razor-Komponenten Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung stellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1b0db66b23c0caffc6b7c4e4af723c020609612a
ms.sourcegitcommit: d5d45d84fe488427d418de770000f7df44a08370
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81539660"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>Globalisierung und Lokalisierung in ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung gestellt werden. Die folgenden Szenarios zur Globalisierung und Lokalisierung von .NET sind verfügbar:

* .NET-Ressourcensystem
* Kulturspezifische Zahlen- und Datumsformatierung

Derzeit werden Lokalisierungsszenarios für ASP.NET Core eingeschränkt unterstützt:

* `IStringLocalizer<>` in Blazor-Apps *wird unterstützt*
* `IHtmlLocalizer<>`, `IViewLocalizer<>` und die Lokalisierung von Datenanmerkungen sind ASP.NET Core MVC-Szenarios und **werden nicht in Blazor-Apps unterstützt**.

Weitere Informationen finden Sie unter <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisierung

Die `@bind`-Funktionalität von Blazor führt Formate durch und analysiert Werte zur Anzeige basierend auf der aktuellen Kultur des Benutzers.

Der Zugriff auf die aktuelle Kultur kann über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft erfolgen.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:

* `date`
* `number`

Diese Feldtypen:

* werden basierend auf ihren entsprechenden browserbasierten Formatierungsregeln angezeigt.
* können keinen Freiformtext enthalten.
* bieten Benutzerinteraktionsmerkmale basierend auf der Implementierung des Browsers.

Die folgenden Feldtypen verfügen über spezifische Formatierungsanforderungen und werden derzeit nicht von Blazor unterstützt, weil sie von keinem der gängigen Browser unterstützt werden:

* `datetime-local`
* `month`
* `week`

`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName>-Klasse zum Analysieren und Formatieren eines Werts. Vom Festlegen einer Kultur wird abgeraten, wenn die Feldtypen `date` und `number` verwendet werden. `date` und `number` verfügen über integrierte Blazor-Unterstützung, die die erforderliche Kultur bereitstellt.

## <a name="localization"></a>Lokalisierung

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Blazor-WebAssembly-Apps legen die Kultur anhand der [Spracheinstellungen](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) des Benutzers fest.

Legen Sie in `Program.Main` `CultureInfo.DefaultThreadCurrentCulture` und `CultureInfo.DefaultThreadCurrentUICulture` fest, um die Kultur explizit zu konfigurieren.

Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas. Weitere Informationen und Anleitungen zum Steuern des Verhaltens des Linkers finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

Auch wenn für die meisten Benutzer die standardmäßig von Blazor ausgewählte Kultur möglicherweise ausreichend ist, ziehen Sie in Betracht, Benutzern die Möglichkeit zu geben, ihr bevorzugtes Gebietsschema anzugeben. Eine Blazor-WebAssembly-Beispiel-App mit Kulturauswahl finden Sie in der [LocSample](https://github.com/pranavkm/LocSample)-Lokalisierungs-Beispiel-App.

### <a name="opno-locblazor-server"></a>Blazor Server

Blazor Server-Apps werden mit [Lokalisierungsmiddleware](xref:fundamentals/localization#localization-middleware) lokalisiert. Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der App anfordern.

Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:

* [Cookies](#cookies)
* [Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur](#provide-ui-to-choose-the-culture)

Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.

#### <a name="cookies"></a>Cookies

Ein Cookie für die Lokalisierungskultur kann die Kultur des Benutzers beibehalten. Das Cookie wird von der `OnGet`-Methode der Hostseite der App erstellt (*Pages/Host.cshtml.cs*). Die Lokalisierungsmiddleware liest das Cookie bei aufeinanderfolgenden Anforderungen, um die Kultur des Benutzers festzulegen. 

Durch Verwendung eines Cookies wird sichergestellt, wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann. Wenn die Lokalisierungsschemas auf dem URL-Pfad oder der Abfragezeichenfolge basieren, kann das Schema möglicherweise nicht mit WebSockets funktionieren, wodurch das Beibehalten der Kultur fehlschlägt. Daher wird die Verwendung eines Cookies für die Lokalisierungskultur empfohlen.

Alle Vorgehensweisen können zum Zuweisen einer Kultur verwendet werden, wenn die Kultur in einem Lokalisierungscookie beibehalten wird. Wenn die App bereits über ein Lokalisierungsschema für serverseitiges ASP.NET Core verfügt, können Sie die vorhandene Lokalisierungsinfrastruktur der App weiterhin verwenden und das Lokalisierungskulturcookie innerhalb des Schemas der App festlegen.

Im folgenden Beispiel wird veranschaulicht, wie die aktuelle Kultur in einem Cookie festgelegt werden kann, das von der Lokalisierungsmiddleware gelesen werden kann. Erstellen Sie eine Datei *Pages/_Host.cshtml.cs* mit dem folgenden Inhalt in der Blazor-Server-App:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Die Lokalisierung wird mit der folgenden Ereignissequenz von der App verarbeitet:

1. Der Browser sendet zunächst eine HTTP-Anforderung an die App.
1. Die Kultur wird von der Lokalisierungsmiddleware zugewiesen.
1. Die `OnGet`-Methode in *_Host.cshtml.cs* speichert die Kultur im Rahmen der Reaktion in einem Cookie.
1. Der Browser stellt eine WebSocket-Verbindung her, um einer interaktive Blazor-Serversitzung zu erstellen.
1. Die Lokalisierungsmiddleware liest das Cookie und weist die Kultur zu.
1. Die Blazor-Serversitzung beginnt mit der richtigen Kultur.

#### <a name="provide-ui-to-choose-the-culture"></a>Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur

Zum Bereitstellen einer Benutzeroberfläche, um Benutzern das Auswählen einer Kultur zu ermöglichen, wird ein *Ansatz auf Grundlage von Umleitungen* empfohlen. Dieses Verfahren ähnelt dem, was in einer Web-App passiert, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen. Der Benutzer wird auf eine Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource. 

Die App behält die vom Benutzer ausgewählte Kultur mithilfe einer Umleitung an einen Controller bei. Der Controller legt die ausgewählte Kultur des Benutzers mit einem Cookie fest und leitet den Benutzer wieder an den ursprünglichen URI weiter.

Erstellen Sie einen HTTP-Endpunkt auf dem Server zum Festlegen der ausgewählten Kultur eines Benutzers in einem Cookie, und leiten Sie ihn dann wieder an den ursprünglichen URI weiter:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Verwenden Sie das Ergebnis der `LocalRedirect`-Aktion, um Angriffe durch offene Umleitungen zu verhindern. Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

Im folgenden Beispiel wird das Durchführen einer Umleitung veranschaulicht, wenn der Benutzer eine Kultur auswählt:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/localization>
