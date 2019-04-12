---
title: Razor-Komponenten, die routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in apps und über die NavLink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/routing
ms.openlocfilehash: ef82fa7e0d571979a43fd8ce712bf4f22c06f9a7
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515549"
---
# <a name="razor-components-routing"></a>Razor-Komponenten, die routing

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Sie Anforderungen in apps und über die NavLink-Komponente weiterleiten.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core-Endpunkt-routing-integration

Razor-Komponenten sind in integriert [routing in ASP.NET Core](xref:fundamentals/routing). Eine ASP.NET Core-app ist so konfiguriert, dass die Annahme eingehender Verbindungen für interaktive Razor-Komponenten mit `MapComponentHub<TComponent>` in `Startup.Configure`. `MapComponentHub` Gibt an, dass die Stammkomponente `App` gerendert werden soll, in ein DOM-Element, das den Selektor übereinstimmende `app`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a>Routenvorlagen

Die `<Router>` Komponente ermöglicht die Weiterleitung, und eine routenvorlage wird bereitgestellt, um jede Komponente zugegriffen werden kann. Die `<Router>` Komponente angezeigt wird, der *Components/App.razor* Datei:

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

Wenn eine *.razor* oder *cshtml* -Datei mit ein `@page` Richtlinie wird kompiliert, wird die generierte Klasse wird eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> die routenvorlage angeben. Zur Laufzeit sucht der Router Komponentenklassen mit einem `RouteAttribute` und rendert, welche Komponente eine routenvorlage verfügt, die mit der angeforderten URL übereinstimmt.

Mehrere routenvorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anfragen für `/BlazorRoute` und `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` unterstützt das Festlegen einer fallback Komponente für das Rendering, wenn eine angeforderte Route nicht aufgelöst werden. Aktivieren Sie dieses Szenario durch Festlegen der `FallbackComponent` Parameter, um den Typ der fallback Komponentenklasse.

Im folgenden Beispiel wird eine Komponente, die in definierten *Pages/MyFallbackRazorComponent.cshtml* als fallback für die Komponente eine `<Router>`:

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> Um Routen ordnungsgemäß generieren zu können, muss die app enthalten eine `<base>` -Tag in die *wwwroot/Index.HTML* -Datei mit den app-Basispfad angegeben, der `href` Attribut (`<base href="/">`). Weitere Informationen finden Sie unter <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.

## <a name="route-parameters"></a>Routenparameter

Der Router verwendet Routenparameter zum Auffüllen der entsprechenden Komponentenparameter mit dem gleichen Namen (Groß-/Kleinschreibung):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

Optionale Parameter werden nicht unterstützt, daher werden zwei `@page` Anweisungen sind im obigen Beispiel angewendet. Die erste ermöglicht die Navigation zu der Komponente ohne Parameter. Die zweite `@page` Direktive nimmt die `{text}` Routenparameter und weist den Wert der `Text` Eigenschaft.

## <a name="route-constraints"></a>Einschränkungen

Eine routeneinschränkung erzwingt typübereinstimmung auf einem routensegment zu einer Komponente.

Im folgenden Beispiel entspricht die Route für die Benutzer-Komponente nur, wenn:

* Ein `Id` routensegment auf die Anforderungs-URL vorhanden ist.
* Die `Id` -Segment ist eine ganze Zahl (`int`).

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

Die routeneinschränkungen, die in der folgenden Tabelle dargestellt sind verfügbar. Die routeneinschränkungen, die mit der invarianten Kultur entsprechen, finden Sie in der Warnung unterhalb der Tabelle Weitere Informationen.

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

## <a name="navlink-component"></a>NavLink-Komponente

Verwenden Sie eine Komponente NavLink anstelle von HTML- `<a>` Elemente beim Erstellen von Navigationslinks. Eine Komponente NavLink verhält sich wie ein `<a>` -Element, mit der Ausnahme umgeschaltet ein `active` CSS-Klasse abhängig davon, ob die `href` mit der aktuelle URL übereinstimmt. Die `active` Klasse hilft, einen Benutzer zu verstehen, welche Seite zur aktiven Seite für die Navigationslinks angezeigt wird.

Die folgende NavMenu-Komponente erstellt ein [Bootstrap](https://getbootstrap.com/docs/) Navigationsleiste, die zeigt, wie NavLink Komponenten verwendet:

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

Es gibt zwei `NavLinkMatch` Optionen:

* `NavLinkMatch.All` &ndash; Gibt an, dass die NavLink aktiv sein sollte, wenn es sich um die gesamte aktuelle URL übereinstimmt.
* `NavLinkMatch.Prefix` &ndash; Gibt an, dass die NavLink aktiv sein sollte, wenn es sich um Präfix des aktuellen URLs übereinstimmt.

Im vorherigen Beispiel ist die Startseite NavLink (`href=""`) entspricht allen URLs und erhält immer das `active` CSS-Klasse. Die zweite NavLink erhält nur die `active` Klasse, wenn der Benutzer die Komponente Blazor Route besucht (`href="BlazorRoute"`).
