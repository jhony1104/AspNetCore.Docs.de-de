---
title: 'Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core'
author: guardrex
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914975"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Verwenden Sie die- `@page` Direktive der Seite, um eine Seiten Route anzugeben, Routen Segmente hinzuzufügen oder einer Route Parameter hinzuzufügen. Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routen Segmente oder Parameternamen verwendet werden können. Weitere Informationen finden [Sie unter Routing: Reservierte Routing](xref:fundamentals/routing#reserved-routing-names)Namen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Razor Pages Konventionen werden mithilfe der <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> -Erweiterungsmethode für <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> die Dienst Auflistung in der `Startup` -Klasse hinzugefügt und konfiguriert. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention(
                    "/About", model => { ... });
                options.Conventions.AddPageRoute(
                    "/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention(
                    "/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention(
                    "/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>Routen Reihenfolge

Routen geben einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung an (Routen Abgleich).

| Auftrag            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird verarbeitet, bevor andere Routen verarbeitet werden. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, `Order` wird die Route für die Verarbeitung standardmäßig auf 0 (null) gesetzt. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge der Route an. |

Die Routen Verarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1 &hellip; , 2, n).
* Wenn Routen identisch `Order`sind, wird die spezifischere Route zuerst abgeglichen, gefolgt von weniger spezifischen Routen.
* Wenn Routen mit der gleichen `Order` Anzahl von Parametern mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>der hinzugefügt werden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Auftrags Verarbeitungsreihenfolge. Im Allgemeinen wählt das Routing die richtige Route mit URL-Übereinstimmung aus. Wenn Sie Routen `Order` Eigenschaften festlegen müssen, um Anforderungen ordnungsgemäß weiterzuleiten, ist das Routing Schema der APP wahrscheinlich verwirrend für Clients und anfällig für die Wartung. Versuchen Sie, das Routing Schema der APP zu vereinfachen. Die Beispiel-App erfordert eine explizite Routen Verarbeitungsreihenfolge, um verschiedene Routing Szenarien mithilfe einer einzelnen APP zu veranschaulichen. Sie sollten jedoch versuchen, die Vorgehensweise beim Festlegen von Route `Order` in Produktions-apps zu vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routen Reihenfolge in den MVC-Themen [finden Sie unter Routing zu Controller Aktionen: Sortieren von Attribut](xref:mvc/controllers/routing#ordering-attribute-routes)Routen.

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modell Konventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die für Razor Pages gelten.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routen Modell Konvention zu allen Seiten

Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung von Seiten Weiterleitungs Modellen angewendet werden, ein zu erstellen und hinzuzufügen

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Weiterleitungs Übereinstimmungs Verhalten in der Beispiel-App sichergestellt:

* Eine Routen Vorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Route der Kontaktseite hat eine Standard Reihenfolge von `null` (`Order = 0`), sodass Sie `{globalTemplate?}` vor der Routen Vorlage übereinstimmt.
* Eine `{aboutTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine `{otherPagesTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *pages/otherpages* `/OtherPages/Page1/RouteDataValue`mit einem Routen Parameter (z. b.) angefordert wird, wird "routedatavalue" in`Order = 1` `RouteData.Values["globalTemplate"]` () und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil das `Order` -Eigenschaft.

Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Razor Pages Optionen (z. b <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>. hinzufügen) werden hinzugefügt, wenn MVC der Dienst `Startup.ConfigureServices`Auflistung in hinzugefügt wird. In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer APP-Modell Konvention zu allen Seiten

Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung von Seiten-App-Modellen angewendet werden, eine hinzuzufügen.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer handlermodellkonvention zu allen Seiten

Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> Sie, um der Auflistung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> von-Instanzen, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> die während der Erstellung des seitenhandlermodells angewendet werden, zu erstellen und hinzuzufügen

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der von abgeleitete Standardrouten Modell Anbieter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die zum Bereitstellen von Erweiterungs Punkten zum Konfigurieren von Seiten Routen entworfen wurden.

### <a name="folder-route-model-convention"></a>Ordner Routen-Modell Konvention

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> Sie, um einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> eine Aktion für alle Seiten im angegebenen Ordner aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass `{globalTemplate?}` die Vorlage für (die zuvor im `1`Thema auf festgelegt wurde) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn eine Seite im Ordner *pages/otherpages* mit einem `/OtherPages/Page1/RouteDataValue`Routen Parameterwert (z. b.) angefordert wird, wird "routedatavalue" in`Order = 1` `RouteData.Values["globalTemplate"]` () und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil der `Order` -Eigenschaft.

Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seiten Weiterleitungs Modell Konvention

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> Sie, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> eine Aktion für die für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass `{globalTemplate?}` die Vorlage für (die zuvor im `1`Thema auf festgelegt wurde) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn die Info-Seite mit einem Routen Parameter `/About/RouteDataValue`Wert unter angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) geladen, weil die `Order` -Eigenschaft festgelegt wurde.

Legen Sie, sofern möglich, `Order`nicht den fest, `Order = 0`der ergibt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden eines parametertransformer zum Anpassen von Seiten Routen

Die von ASP.net Core generierten Seiten Routen können mit einem Parameter Transformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `PageRouteTransformerConvention` Page Route-Modell Konvention wendet einen Parameter Transformator auf die Ordner-und Dateinamen Segmente von automatisch generierten Seiten Routen in einer APP an. Beispielsweise wird in der Razor Pages-Datei unter */pages/SubscriptionManagement/ViewAll.cshtml* von `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all`die Route umgeschrieben.

`PageRouteTransformerConvention`transformiert nur die automatisch generierten Segmente einer Seiten Route, die aus dem Razor Pages Ordner und dem Dateinamen stammen. Mit der `@page` -Direktive hinzugefügte Routen Segmente werden nicht transformiert. Außerdem werden von hinzugefügte Routen von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>der Konvention nicht transformiert.

Die `PageRouteTransformerConvention` ist als Option in `Startup.ConfigureServices`registriert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
                        new SlugifyParameterTransformer()));
            });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Verwenden <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> Sie, um eine Route zu einer Seite am angegebenen Seiten Pfad zu konfigurieren. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seiten Modelle, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> der implementiert, ruft Konventionen auf, die entwickelt wurden, um Erweiterungs Punkte zum Konfigurieren von Seiten Modellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet `AddHeaderAttribute` die Beispiel-App eine-Klasse, bei der <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>es sich um einen handelt, der einen Antwortheader anwendet:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> Sie, um einen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> , der eine Aktion für-Instanzen für alle Seiten unter dem angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> Sie, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> eine Aktion für die für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguriert den angegebenen Filter, der angewendet werden soll. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Da Aktionsfilter von Razor Pages ignoriert werden, wirkt `EmptyFilter` sich das nicht wie vorgesehen aus, wenn der Pfad `OtherPages/Page2`nicht enthält.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguriert die angegebene Factory so, dass [Filter](xref:mvc/controllers/filters) auf alle Razor Pages angewendet werden.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

*AddHeaderWithFactory.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden. Andere Typen von MVC-Filtern stehen zur Verwendung zur Verfügung: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters)und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seiten Filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der für Razor Pages gilt. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
