---
title: ASP.net Core Blazor Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die navlink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 0cd15f25ff7975cae3f63a739212aa23062ece23
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160157"
---
# <a name="aspnet-core-opno-locblazor-routing"></a>ASP.net Core Blazor Routing

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Erfahren Sie, wie Sie Anforderungen weiterleiten und wie Sie die `NavLink` Komponente verwenden, um Navigations Verknüpfungen in Blazor-apps zu erstellen.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.net Core Endpunkt-Routing Integration

Blazor Server ist in [ASP.net Core Endpunkt Routing](xref:fundamentals/routing)integriert. Eine ASP.net Core-APP ist so konfiguriert, dass eingehende Verbindungen für interaktive Komponenten mit `MapBlazorHub` in `Startup.Configure`akzeptiert werden:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Die typische Konfiguration besteht darin, alle Anforderungen an eine Razor Page weiterzuleiten, die als Host für den serverseitigen Teil der Blazor Server-App fungiert. Gemäß der Konvention wird die *Hostseite* in der Regel *_Host. cshtml*benannt. Die in der Hostdatei angegebene Route wird als *Fall Back Route* bezeichnet, da Sie bei der Routen Übereinstimmung mit niedriger Priorität arbeitet. Die Fall Back Route wird berücksichtigt, wenn andere Routen nicht gleich sind. Dies ermöglicht der APP, andere Controller und Seiten zu verwenden, ohne die Blazor Server-APP zu beeinträchtigen.

## <a name="route-templates"></a>Routen Vorlagen

Die `Router` Komponente ermöglicht das Routing an jede Komponente mit einer angegebenen Route. Die `Router` Komponente wird in der Datei " *app. Razor* " angezeigt:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Wenn eine *Razor* -Datei mit einer `@page`-Direktive kompiliert wird, wird der generierten Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt, bereitgestellt.

Zur Laufzeit wird die `RouteView` Komponente:

* Empfängt die `RouteData` zusammen mit den gewünschten Parametern vom `Router`.
* Rendert die angegebene Komponente mit dem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.

Optional können Sie einen `DefaultLayout`-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout angeben. In den standardmäßigen Blazor Vorlagen wird die `MainLayout` Komponente angegeben. *MainLayout. Razor* befindet sich im frei *gegebenen* Ordner des Vorlagen Projekts. Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.

Mehrere Routen Vorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Damit URLs ordnungsgemäß aufgelöst werden, muss die APP ein `<base>`-Tag in Ihrer *wwwroot/Index.html* -Datei (Blazor Webassembly) oder in der Datei *pages/_Host. cshtml* (Blazor Server) mit dem App-Basispfad enthalten, der im `href`-Attribut (`<base href="/">`) angegeben ist. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Benutzerdefinierten Inhalt bereitstellen, wenn Inhalt nicht gefunden wird

Die `Router` Komponente ermöglicht der APP, benutzerdefinierten Inhalt anzugeben, wenn der Inhalt für die angeforderte Route nicht gefunden wurde.

Legen Sie in der Datei " *app. Razor* " benutzerdefinierten Inhalt im `NotFound` Template-Parameter der `Router` Komponente fest:

```razor
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

Der Inhalt `<NotFound>` Tags kann beliebige Elemente enthalten, wie z. b. andere interaktive Komponenten. Informationen zum Anwenden eines Standard Layouts auf `NotFound` Inhalt finden Sie unter <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Weiterleiten an Komponenten aus mehreren Assemblys

Verwenden Sie den `AdditionalAssemblies`-Parameter, um zusätzliche Assemblys anzugeben, die die `Router` Komponente bei der Suche nach Routing fähigen Komponenten beachten soll. Angegebene Assemblys werden zusätzlich zur `AppAssembly`angegebenen Assembly berücksichtigt. Im folgenden Beispiel ist `Component1` eine Routing fähige Komponente, die in einer referenzierten Klassenbibliothek definiert ist. Im folgenden `AdditionalAssemblies` Beispiel wird die Routing Unterstützung für `Component1`:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Routen Parameter

Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter mit dem gleichen Namen (ohne Beachtung der Groß-/Kleinschreibung) aufzufüllen:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Optionale Parameter werden nicht unterstützt. Im vorherigen Beispiel werden zwei `@page` Direktiven angewendet. Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter. Die zweite `@page`-Direktive übernimmt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.

## <a name="route-constraints"></a>Routen Einschränkungen

Eine Routen Einschränkung erzwingt die Typübereinstimmung in einem Routen Segment zu einer Komponente.

Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur mit überein, wenn:

* Ein `Id` Routen Segment ist in der Anforderungs-URL vorhanden.
* Das `Id` Segment ist eine Ganzzahl (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

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

In Blazor Server-Apps ist die Standardroute in *_Host. cshtml* `/` (`@page "/"`). Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei erscheint. Eine Blazor-App gibt eine *404-nicht gefundene* Antwort für eine statische Datei zurück, die nicht vorhanden ist. Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host. cshtml* mit der folgenden Routen Vorlage:

```cshtml
@page "/{**path}"
```

Die `"/{**path}"` Vorlage umfasst Folgendes:

* Doppelte Sternchen *-catch-all-* Syntax (`**`), um den Pfad über mehrere Ordner Grenzen ohne Codierungs Schrägstriche (`/`) zu erfassen.
* `path` Routen Parameter Name.

> [!NOTE]
> Die *catch-all-* Parameter Syntax (`*`/`**`) wird in Razor-Komponenten *(Razor*-Komponenten) **nicht** unterstützt.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Navlink-Komponente

Verwenden Sie beim Erstellen von Navigationslinks eine `NavLink` Komponente anstelle von HTML-Hyperlink-Elementen (`<a>`). Eine `NavLink` Komponente verhält sich wie ein `<a>` Element, mit dem Unterschied, dass eine `active` CSS-Klasse auf der Grundlage der entsprechenden `href` mit der aktuellen URL umgeschaltet wird. Die `active`-Klasse hilft einem Benutzer zu verstehen, welche Seite die aktive Seite unter den angezeigten Navigationslinks ist.

Die folgende `NavMenu` Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/) -Navigationsleiste, die die Verwendung `NavLink` Komponenten veranschaulicht:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Es gibt zwei `NavLinkMatch` Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:

* `NavLinkMatch.All` &ndash; die `NavLink` aktiv ist, wenn Sie mit der gesamten aktuellen URL übereinstimmt.
* `NavLinkMatch.Prefix` (*Standard*) &ndash; das `NavLink` aktiv ist, wenn es mit einem beliebigen Präfix der aktuellen URL übereinstimmt.

Im vorherigen Beispiel ist der Home `NavLink` `href=""` mit der Home-URL übereinstimmt und empfängt nur die `active` CSS-Klasse in der Standard-Basispfad-URL der APP (z. b. `https://localhost:5001/`). Die zweite `NavLink` empfängt die `active`-Klasse, wenn der Benutzer eine URL mit einem `MyComponent` Präfix (z. b. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`) besucht.

Zusätzliche `NavLink` Komponenten Attribute werden an das gerenderte Ankertag weitergegeben. Im folgenden Beispiel enthält die `NavLink` Komponente das `target`-Attribut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Das folgende HTML-Markup wird gerendert:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>URI-und Navigations Zustands Hilfen

Verwenden Sie `Microsoft.AspNetCore.Components.NavigationManager`, um mit URIs und der C# Navigation in Code zu arbeiten. `NavigationManager` stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.

| Member | Beschreibung |
| ------ | ----------- |
| `Uri` | Ruft den aktuellen absoluten URI ab. |
| `BaseUri` | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangesteht werden kann, um einen absoluten URI zu erhalten. In der Regel entspricht `BaseUri` dem `href`-Attribut im `<base>`-Element des Dokuments in *wwwroot/Index.html* (Blazor Webassembly) oder *pages/_Host. cshtml* (Blazor Server). |
| `NavigateTo` | Navigiert zum angegebenen URI. Wenn `forceLoad` `true`:<ul><li>Client seitiges Routing wird umgangen.</li><li>Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom Client seitigen Router verarbeitet wird.</li></ul> |
| `LocationChanged` | Ein Ereignis, das ausgelöst wird, wenn sich die Navigations Position geändert hat. |
| `ToAbsoluteUri` | Konvertiert einen relativen URI in einen absoluten URI. |
| `ToBaseRelativePath` | Wenn ein Basis-URI (z. b. ein URI, der zuvor von `GetBaseUri`zurückgegeben wurde) angegeben wurde, konvertiert einen absoluten URI in einen URI relativ zum Basis-URI-Präfix. |

Wenn die Schaltfläche ausgewählt wird, navigiert die folgende Komponente zur `Counter` Komponente der APP:

```razor
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
