---
title: ASP.net Core blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/routing
ms.openlocfilehash: 197b1a91b3540d21639c3ee775b2c490da7b23fe
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030393"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.net Core blazor-Routing

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Sie Anforderungen weiterleiten und wie `NavLink` Sie die-Komponente verwenden, um Navigations Verknüpfungen in blazor-apps zu erstellen.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.net Core Endpunkt-Routing Integration

Der serverseitige blazor ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert. Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für `MapBlazorHub` interaktive `Startup.Configure`Komponenten mit in akzeptiert werden:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Routen Vorlagen

Die `Router` Komponente aktiviert das Routing, und jeder zugänglichen Komponente wird eine Routen Vorlage bereitgestellt. Die `Router` Komponente wird in der Datei " *app. Razor* " angezeigt:

In einer serverseitigen blazor-App:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

In einer Client seitigen blazor-App:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

Wenn eine *Razor* -Datei mit einer `@page` -Direktive kompiliert wird, wird der generierten Klasse <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> eine bereitgestellt, die die Routen Vorlage angibt. Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert die Komponente mit einer Routen Vorlage, die mit der angeforderten URL übereinstimmt.

Mehrere Routen Vorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Um Routen ordnungsgemäß zu generieren, muss die APP `<base>` ein Tag in der *wwwroot/Index.html* -Datei (Client seitige Blade) oder *pages/_Host. cshtml* -Datei (blazor serverseitig) mit dem im- `href` Attribut angegebenen app-Basispfad enthalten ( `<base href="/">`). Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/client-side#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird

Die `Router` -Komponente ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.

Legen Sie in der Datei " *app. Razor* " benutzerdefinierten `<NotFoundContent>` Inhalt im- `Router` Element der-Komponente fest:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

Der Inhalt von `<NotFoundContent>` kann beliebige Elemente enthalten, wie z. b. andere interaktive Komponenten.

## <a name="route-parameters"></a>Routen Parameter

Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter mit dem gleichen Namen (ohne Beachtung der Groß-/Kleinschreibung) aufzufüllen:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Optionale Parameter werden für blazor-apps in ASP.net Core 3,0 Preview nicht unterstützt. Im `@page` vorherigen Beispiel werden zwei-Direktiven angewendet. Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter. Die zweite `@page` Anweisung übernimmt den `{text}` Routen Parameter und `Text` weist den Wert der-Eigenschaft zu.

## <a name="route-constraints"></a>Routen Einschränkungen

Eine Routen Einschränkung erzwingt die Typübereinstimmung in einem Routen Segment zu einer Komponente.

Im folgenden Beispiel entspricht die Route an die `Users` -Komponente nur, wenn:

* Ein `Id` Routen Segment ist in der Anforderungs-URL vorhanden.
* Das `Id` Segment ist eine ganze Zahl`int`().

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Die in der folgenden Tabelle aufgeführten Routen Einschränkungen sind verfügbar. Informationen zu den Routen Einschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.

| Constraint | Beispiel           | Beispiele für Übereinstimmungen                                                                  | Invariante<br>culture<br>Übereinstimmend |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nein                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ja                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ja                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ja                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ja                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nein                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ja                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ja                              |

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.

## <a name="navlink-component"></a>Navlink-Komponente

Verwenden Sie `NavLink` beim Erstellen von Navigationslinks eine Komponente anstelle`<a>`von HTML-Hyperlink-Elementen (). Eine `NavLink` -Komponente verhält sich `<a>` wie ein-Element, mit der Ausnahme `active` , dass Sie eine CSS- `href` Klasse umschaltet, je nachdem, ob Ihre der aktuellen URL entspricht. Mithilfe `active` der-Klasse kann ein Benutzer verstehen, welche Seite die aktive Seite unter den angezeigten Navigationslinks ist.

Die folgende `NavMenu` Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/) -Navigationsleiste, die die Verwendung `NavLink` von-Komponenten veranschaulicht:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Es gibt zwei `NavLinkMatch` Optionen, die Sie dem `Match` -Attribut des `<NavLink>` -Elements zuweisen können:

* `NavLinkMatch.All`&ndash; Der`NavLink` ist aktiv, wenn er mit der gesamten aktuellen URL übereinstimmt.
* `NavLinkMatch.Prefix`(*Standard*) &ndash; Der`NavLink` ist aktiv, wenn er mit einem beliebigen Präfix der aktuellen URL übereinstimmt.

Im vorherigen `NavLink` Beispiel stimmt die Start `href=""` -URL mit der Home-URL überein und `active` empfängt nur die CSS-Klasse an der `https://localhost:5001/`Standard-Basispfad-URL der APP (z. b.). Die zweite `NavLink` empfängt die `active` -Klasse, wenn der Benutzer eine URL mit `MyComponent` einem Präfix (z `https://localhost:5001/MyComponent` . `https://localhost:5001/MyComponent/AnotherSegment`b. und) besucht.

Zusätzliche `NavLink` Komponenten Attribute werden an das gerenderte Ankertag weitergeleitet. Im folgenden Beispiel enthält die `NavLink` -Komponente das `target` -Attribut:

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Das folgende HTML-Markup wird gerendert:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI-und Navigations Zustands Hilfen

Verwenden `Microsoft.AspNetCore.Components.IUriHelper` Sie, um mit URIs und der C# Navigation in Code zu arbeiten. `IUriHelper`bietet das Ereignis und die Methoden, die in der folgenden Tabelle aufgeführt sind.

| Member | Beschreibung |
| ------ | ----------- |
| `GetAbsoluteUri` | Ruft den aktuellen absoluten URI ab. |
| `GetBaseUri` | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten. `<base>` `href` `GetBaseUri` In der Regel entspricht dem-Attribut im-Element des Dokuments in *wwwroot/Index.html* (Blade Client-Side) oder *pages/_Host. cshtml* (serverseitiges Blade von blazor). |
| `NavigateTo` | Navigiert zum angegebenen URI. Wenn `forceLoad` ist `true`:<ul><li>Client seitiges Routing wird umgangen.</li><li>Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</li></ul> |
| `OnLocationChanged` | Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat. |
| `ToAbsoluteUri` | Konvertiert einen relativen URI in einen absoluten URI. |
| `ToBaseRelativePath` | Wenn ein Basis-URI (z. b. ein URI, `GetBaseUri`der zuvor von zurückgegeben wurde) angegeben wird, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix. |

Wenn die Schaltfläche ausgewählt wird, navigiert `Counter` die folgende Komponente zur Komponente der APP:

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
