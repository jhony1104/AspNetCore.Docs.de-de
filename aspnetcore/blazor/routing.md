---
title: ASP.net Core blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: 6d9d1614b6e0cc9f4711de0db4513ada4841809f
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168177"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.net Core blazor-Routing

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Erfahren Sie, wie Sie Anforderungen weiterleiten und wie `NavLink` Sie die-Komponente verwenden, um Navigations Verknüpfungen in blazor-apps zu erstellen.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.net Core Endpunkt-Routing Integration

Der blazor-Server ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert. Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für `MapBlazorHub` interaktive `Startup.Configure`Komponenten mit in akzeptiert werden:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Routen Vorlagen

Die `Router` Komponente ermöglicht das Routing an jede Komponente mit einer angegebenen Route. Die `Router` Komponente wird in der Datei " *app. Razor* " angezeigt:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Wenn eine *Razor* -Datei mit einer `@page` -Direktive kompiliert wird, wird der generierten Klasse <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> eine bereitgestellt, die die Routen Vorlage angibt.

Zur Laufzeit ist die `RouteView` -Komponente:

* Empfängt den `RouteData` von der `Router` zusammen mit den gewünschten Parametern.
* Rendert die angegebene Komponente mit dem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.

Optional können Sie einen `DefaultLayout` Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout angeben. Die Standardvorlagen für blazor geben `MainLayout` die Komponente an. *MainLayout. Razor* befindet sich im frei *gegebenen* Ordner des Vorlagen Projekts. Weitere Informationen zu Layouts finden <xref:blazor/layouts>Sie unter.

Mehrere Routen Vorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Damit URLs ordnungsgemäß aufgelöst werden, muss die APP ein `<base>` Tag in der *wwwroot/Index.html* -Datei (blazor Webassembly) oder in der Datei *pages/_Host. cshtml* (Blade Server) mit dem `href` App-Basispfad enthalten, der im-Attribut angegeben ist (`<base href="/">`). Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird

Die `Router` -Komponente ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.

Legen Sie in der Datei " *app. Razor* " benutzerdefinierten `NotFound` Inhalt im `Router` Vorlagen Parameter der Komponente fest:

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Der Inhalt von `<NotFound>` Tags kann beliebige Elemente enthalten, wie z. b. andere interaktive Komponenten. Informationen zum Anwenden eines Standard Layouts `NotFound` auf Inhalt finden <xref:blazor/layouts>Sie unter.

## <a name="route-to-components-from-multiple-assemblies"></a>Weiterleiten an Komponenten aus mehreren Assemblys

Verwenden Sie `AdditionalAssemblies` den-Parameter, um zusätzliche Assemblys anzugeben, die bei der `Router` Suche nach Routing fähigen Komponenten in Erwägung gezogen werden sollen. Angegebene Assemblys werden zusätzlich zur `AppAssembly`-angegebenen Assembly berücksichtigt. Im folgenden Beispiel ist eine `Component1` Routing fähige Komponente, die in einer referenzierten Klassenbibliothek definiert ist. Das folgende `AdditionalAssemblies` Beispiel führt zu einer Routing Unterstützung `Component1`für:

< routerappassembly = "typeof (Programm). Assembly "additionalassemblys =" New [] {typeof (Component1). Assembly} >...</Router>

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

| Constraint | Beispiel           | Beispiele für Übereinstimmungen                                                                  | Invariante<br>Kultur<br>Übereinstimmend |
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

### <a name="routing-with-urls-that-contain-dots"></a>Routing mit URLs, die Punkte enthalten

In blazor-Server-Apps ist `/` die Standardroute in *_Host. cshtml* (`@page "/"`). Eine Anforderungs-URL, die einen Punkt`.`() enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei erscheint. Eine blazor-App gibt eine " *404-nicht gefunden"-* Antwort für eine statische Datei zurück, die nicht vorhanden ist. Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host. cshtml* mit der folgenden Routen Vorlage:

```cshtml
@page "/{**path}"
```

Die `"/{**path}"` Vorlage umfasst Folgendes:

* Doppelte Sternchen *-catch-all-* Syntax`**`() zum Erfassen des Pfads über mehrere Ordner Grenzen ohne Codierungs schräg`/`Striche ().
* Ein `path` Routen Parameter Name.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

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

Verwenden `Microsoft.AspNetCore.Components.NavigationManager` Sie, um mit URIs und der C# Navigation in Code zu arbeiten. `NavigationManager`bietet das Ereignis und die Methoden, die in der folgenden Tabelle aufgeführt sind.

| Member | Beschreibung |
| ------ | ----------- |
| `Uri` | Ruft den aktuellen absoluten URI ab. |
| `BaseUri` | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten. `<base>` `href` `BaseUri` In der Regel entspricht dem-Attribut im-Element des Dokuments in *wwwroot/Index.html* (blazor Webassembly) oder *pages/_Host. cshtml* (blazor Server). |
| `NavigateTo` | Navigiert zum angegebenen URI. Wenn `forceLoad` ist `true`:<ul><li>Client seitiges Routing wird umgangen.</li><li>Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</li></ul> |
| `LocationChanged` | Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat. |
| `ToAbsoluteUri` | Konvertiert einen relativen URI in einen absoluten URI. |
| `ToBaseRelativePath` | Wenn ein Basis-URI (z. b. ein URI, `GetBaseUri`der zuvor von zurückgegeben wurde) angegeben wird, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix. |

Wenn die Schaltfläche ausgewählt wird, navigiert `Counter` die folgende Komponente zur Komponente der APP:

```cshtml
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```
