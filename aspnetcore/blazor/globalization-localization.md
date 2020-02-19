---
title: ASP.net Core Blazor Globalisierung und Lokalisierung
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten für Benutzer in mehreren Kulturen und Sprachen zugänglich machen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453187"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.net Core Blazor Globalisierung und Lokalisierung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können Benutzern in mehreren Kulturen und Sprachen zugänglich gemacht werden. Die folgenden Szenarien für die .NET-Globalisierung und-Lokalisierung sind verfügbar:

* . NET-Ressourcensystem
* Kulturspezifische Zahlen-und Datums Formatierung

Zurzeit werden begrenzte Lokalisierungs Szenarien ASP.net Core unterstützt:

* `IStringLocalizer<>` wird in Blazor-apps *unterstützt* .
* die Lokalisierung von `IHtmlLocalizer<>`, `IViewLocalizer<>`und Daten Anmerkungen ist ASP.net Core MVC-Szenarien und wird in Blazor-apps **nicht unterstützt** .

Weitere Informationen finden Sie unter <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisierung

die `@bind` Funktionalität von Blazorführt Formate aus und analysiert Werte für die Anzeige basierend auf der aktuellen Kultur des Benutzers.

Der Zugriff auf die aktuelle Kultur ist über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft möglich.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:

* `date`
* `number`

Die vorangehenden Feldtypen:

* Werden mithilfe der entsprechenden browserbasierten Formatierungs Regeln angezeigt.
* Darf keinen frei Form Text enthalten.
* Stellen Sie die Eigenschaften der Benutzerinteraktion basierend auf der Implementierung des Browsers bereit.

Die folgenden Feldtypen weisen bestimmte Formatierungs Anforderungen auf und werden zurzeit nicht von Blazor unterstützt, da Sie nicht von allen wichtigen Browsern unterstützt werden:

* `datetime-local`
* `month`
* `week`

`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName> zum Auswerten und Formatieren eines Werts bereitzustellen. Die Angabe einer Kultur ist nicht empfehlenswert, wenn die Feldtypen `date` und `number` verwendet werden. `date` und `number` verfügen über integrierte Blazor Unterstützung, die die erforderliche Kultur bereitstellt.

## <a name="localization"></a>Lokalisierung

Blazor Server-apps werden mithilfe von [Lokalisierungs Middleware](xref:fundamentals/localization#localization-middleware)lokalisiert. Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der APP anfordern.

Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:

* [Cookies](#cookies)
* [Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur](#provide-ui-to-choose-the-culture)

Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Konfigurieren des Linkers für die Internationalisierung (Blazor Webassembly)

Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas. Weitere Informationen und Anleitungen zum Steuern des linkerverhaltens finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Cookies

Ein Lokalisierungs Kultur Cookie kann die Kultur des Benutzers beibehalten. Das Cookie wird durch die `OnGet`-Methode der Hostseite der APP (*pages/Host. cshtml. cs*) erstellt. Die Lokalisierungs Middleware liest das Cookie bei nachfolgenden Anforderungen, um die Kultur des Benutzers festzulegen. 

Durch die Verwendung eines Cookies wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann. Wenn Lokalisierungs Schemas auf dem URL-Pfad oder der Abfrage Zeichenfolge basieren, ist das Schema möglicherweise nicht in der Lage, mit websockets zu arbeiten. Daher kann die Kultur nicht persistent gespeichert werden. Daher ist die Verwendung eines Lokalisierungs Kultur Cookies die empfohlene Vorgehensweise.

Jede Technik kann verwendet werden, um eine Kultur zuzuweisen, wenn die Kultur in einem Lokalisierungs Cookie beibehalten wird. Wenn die APP bereits über ein festgelegtes Lokalisierungs Schema für serverseitige ASP.net Core verfügt, verwenden Sie weiterhin die vorhandene Lokalisierungs Infrastruktur der APP, und legen Sie das Lokalisierungs Kultur Cookie innerhalb des App-Schemas fest.

Im folgenden Beispiel wird gezeigt, wie die aktuelle Kultur in einem Cookie festgelegt wird, das von der Lokalisierungs Middleware gelesen werden kann. Erstellen Sie eine Datei *pages/Host. cshtml. cs* mit folgendem Inhalt in der Blazor Server-App:

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

Die Lokalisierung wird von der app in der folgenden Ereignis Sequenz behandelt:

1. Der Browser sendet eine anfängliche http-Anforderung an die app.
1. Die Kultur wird von der Lokalisierungs Middleware zugewiesen.
1. Die `OnGet`-Methode in *_Host. cshtml. cs* speichert die Kultur in einem Cookie als Teil der Antwort.
1. Der Browser öffnet eine WebSocket-Verbindung, um eine interaktive Blazor Server-Sitzung zu erstellen.
1. Die Lokalisierungs Middleware liest das Cookie und weist die Kultur zu.
1. Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.

### <a name="provide-ui-to-choose-the-culture"></a>Bereitstellen der Benutzeroberfläche zum Auswählen der Kultur

Zum Bereitstellen einer Benutzeroberfläche, mit der Benutzer eine Kultur auswählen können, wird ein *Umleitungs basierter Ansatz* empfohlen. Der Prozess ähnelt dem, was in einer Web-App geschieht, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen,&mdash;der Benutzer zu einer Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource umgeleitet wird. 

Die APP speichert die ausgewählte Kultur des Benutzers über eine Umleitung zu einem Controller. Der Controller legt die ausgewählte Kultur des Benutzers in einem Cookie fest und leitet den Benutzer zurück an den ursprünglichen URI.

Richten Sie einen HTTP-Endpunkt auf dem Server ein, um die ausgewählte Kultur des Benutzers in einem Cookie festzulegen, und führen Sie die Umleitung zurück zum ursprünglichen URI aus:

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
> Verwenden Sie das Ergebnis der `LocalRedirect` Aktion, um Open Redirect-Angriffe zu verhindern. Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

Die folgende Komponente zeigt ein Beispiel dafür, wie die anfängliche Umleitung durchgeführt wird, wenn der Benutzer eine Kultur auswählt:

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
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:fundamentals/localization>
