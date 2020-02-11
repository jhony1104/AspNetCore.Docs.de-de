---
title: 'Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core'
author: guardrex
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114769"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Verwenden Sie zum Angeben einer Seiten Route, zum Hinzufügen von Routen Segmenten oder zum Hinzufügen von Parametern zu einer Route die `@page` Direktive der Seite. Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routen Segmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: reservierte Routing Namen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Razor Pages Konventionen werden mithilfe der <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>-Erweiterungsmethode zum <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> der Dienst Auflistung in der `Startup`-Klasse hinzugefügt und konfiguriert. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

| Order            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird verarbeitet, bevor andere Routen verarbeitet werden. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird die Route für die Verarbeitung standardmäßig auf 0 (null) `Order`. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge der Route an. |

Die Routen Verarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen denselben `Order`haben, wird die spezifischere Route zuerst abgeglichen, gefolgt von weniger spezifischen Routen.
* Wenn Routen mit dem gleichen `Order` und der gleichen Anzahl von Parametern mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der Sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>hinzugefügt werden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Auftrags Verarbeitungsreihenfolge. Im Allgemeinen wählt das Routing die richtige Route mit URL-Übereinstimmung aus. Wenn Sie Routen `Order` Eigenschaften festlegen müssen, um Anforderungen ordnungsgemäß weiterzuleiten, ist das Routing Schema der APP wahrscheinlich verwirrend für Clients und anfällig für die Wartung. Versuchen Sie, das Routing Schema der APP zu vereinfachen. Die Beispiel-App erfordert eine explizite Routen Verarbeitungsreihenfolge, um verschiedene Routing Szenarien mithilfe einer einzelnen APP zu veranschaulichen. Sie sollten jedoch versuchen, die Vorgehensweise beim Festlegen von Routen `Order` in Produktions-apps zu vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routen Reihenfolge in den MVC-Themen finden [Sie unter Routing zu Controller Aktionen: Sortieren von Attribut Routen](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modell Konventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die für Razor Pages gelten.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routen Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des Seiten Routen Modells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Weiterleitungs Übereinstimmungs Verhalten in der Beispiel-App sichergestellt:

* Eine Routen Vorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Route der Kontaktseite hat eine Standard Reihenfolge `null` (`Order = 0`), sodass Sie vor der `{globalTemplate?}` Routen Vorlage übereinstimmt.
* Eine `{aboutTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine `{otherPagesTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameter (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Razor Pages Optionen, wie z. b. das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn MVC der Dienst Sammlung in `Startup.ConfigureServices`hinzugefügt wird. In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer APP-Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Instanzen, die während der Erstellung von Seiten-App-Modellen angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des seitenhandlermodells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zu erstellen.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardrouten Modell Anbieter, der von abgeleitet wird <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die zum Bereitstellen von Erweiterungs Punkten zum Konfigurieren von Seiten Routen entworfen wurden.

### <a name="folder-route-model-convention"></a>Ordner Routen-Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die für alle Seiten im angegebenen Ordner eine Aktion auf dem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameterwert (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seiten Weiterleitungs Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn auf `/About/RouteDataValue`die Seite "Info" mit einem Routen Parameterwert angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden eines parametertransformer zum Anpassen von Seiten Routen

Die von ASP.net Core generierten Seiten Routen können mit einem Parameter Transformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `PageRouteTransformerConvention` Page-Weiterleitungs Modell Konvention wendet einen parametertransformator auf die Ordner-und Dateinamen Segmente von automatisch generierten Seiten Routen in einer APP an. Beispielsweise würde die Razor Pages Datei bei */pages/SubscriptionManagement/ViewAll.cshtml* aus `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all`umgeschrieben werden.

`PageRouteTransformerConvention` transformiert nur die automatisch generierten Segmente einer Seiten Route, die aus dem Razor Pages Ordner und dem Dateinamen stammen. Mit der `@page`-Direktive hinzugefügte Routen Segmente werden nicht transformiert. Außerdem werden von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>hinzugefügte Routen nicht durch die Konvention transformiert.

Der `PageRouteTransformerConvention` wird als Option in `Startup.ConfigureServices`registriert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, um eine Route zu einer Seite am angegebenen Seiten Pfad zu konfigurieren. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für `/TheContactPage`Contact.cshtml*eine Route zur Seite*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seiten Modelle, der implementiert <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungs Punkte zum Konfigurieren von Seiten Modellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispiel-App eine `AddHeaderAttribute`-Klasse, bei der es sich um einen <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>handelt, der einen Antwortheader anwendet:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten unter dem angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert den angegebenen Filter für die Anwendung. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Da Aktionsfilter von Razor Pages ignoriert werden, hat die `EmptyFilter` keine Auswirkung, wenn der Pfad nicht `OtherPages/Page2`enthält.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert die angegebene Factory so, dass [Filter](xref:mvc/controllers/filters) auf alle Razor Pages angewendet werden.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden. Sie können jedoch andere Arten von MVC-Filtern verwenden: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seiten Filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der für Razor Pages gilt. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Verwenden Sie zum Angeben einer Seiten Route, zum Hinzufügen von Routen Segmenten oder zum Hinzufügen von Parametern zu einer Route die `@page` Direktive der Seite. Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routen Segmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: reservierte Routing Namen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Razor Pages Konventionen werden mithilfe der <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>-Erweiterungsmethode zum <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> der Dienst Auflistung in der `Startup`-Klasse hinzugefügt und konfiguriert. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

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

| Order            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird verarbeitet, bevor andere Routen verarbeitet werden. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird die Route für die Verarbeitung standardmäßig auf 0 (null) `Order`. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge der Route an. |

Die Routen Verarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen denselben `Order`haben, wird die spezifischere Route zuerst abgeglichen, gefolgt von weniger spezifischen Routen.
* Wenn Routen mit dem gleichen `Order` und der gleichen Anzahl von Parametern mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der Sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>hinzugefügt werden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Auftrags Verarbeitungsreihenfolge. Im Allgemeinen wählt das Routing die richtige Route mit URL-Übereinstimmung aus. Wenn Sie Routen `Order` Eigenschaften festlegen müssen, um Anforderungen ordnungsgemäß weiterzuleiten, ist das Routing Schema der APP wahrscheinlich verwirrend für Clients und anfällig für die Wartung. Versuchen Sie, das Routing Schema der APP zu vereinfachen. Die Beispiel-App erfordert eine explizite Routen Verarbeitungsreihenfolge, um verschiedene Routing Szenarien mithilfe einer einzelnen APP zu veranschaulichen. Sie sollten jedoch versuchen, die Vorgehensweise beim Festlegen von Routen `Order` in Produktions-apps zu vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routen Reihenfolge in den MVC-Themen finden [Sie unter Routing zu Controller Aktionen: Sortieren von Attribut Routen](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modell Konventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die für Razor Pages gelten.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routen Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des Seiten Routen Modells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Weiterleitungs Übereinstimmungs Verhalten in der Beispiel-App sichergestellt:

* Eine Routen Vorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Route der Kontaktseite hat eine Standard Reihenfolge `null` (`Order = 0`), sodass Sie vor der `{globalTemplate?}` Routen Vorlage übereinstimmt.
* Eine `{aboutTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine `{otherPagesTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameter (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Razor Pages Optionen, wie z. b. das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn MVC der Dienst Sammlung in `Startup.ConfigureServices`hinzugefügt wird. In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer APP-Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Instanzen, die während der Erstellung von Seiten-App-Modellen angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des seitenhandlermodells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zu erstellen.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardrouten Modell Anbieter, der von abgeleitet wird <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die zum Bereitstellen von Erweiterungs Punkten zum Konfigurieren von Seiten Routen entworfen wurden.

### <a name="folder-route-model-convention"></a>Ordner Routen-Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die für alle Seiten im angegebenen Ordner eine Aktion auf dem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameterwert (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seiten Weiterleitungs Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn auf `/About/RouteDataValue`die Seite "Info" mit einem Routen Parameterwert angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden eines parametertransformer zum Anpassen von Seiten Routen

Die von ASP.net Core generierten Seiten Routen können mit einem Parameter Transformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `PageRouteTransformerConvention` Page-Weiterleitungs Modell Konvention wendet einen parametertransformator auf die Ordner-und Dateinamen Segmente von automatisch generierten Seiten Routen in einer APP an. Beispielsweise würde die Razor Pages Datei bei */pages/SubscriptionManagement/ViewAll.cshtml* aus `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all`umgeschrieben werden.

`PageRouteTransformerConvention` transformiert nur die automatisch generierten Segmente einer Seiten Route, die aus dem Razor Pages Ordner und dem Dateinamen stammen. Mit der `@page`-Direktive hinzugefügte Routen Segmente werden nicht transformiert. Außerdem werden von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>hinzugefügte Routen nicht durch die Konvention transformiert.

Der `PageRouteTransformerConvention` wird als Option in `Startup.ConfigureServices`registriert:

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

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, um eine Route zu einer Seite am angegebenen Seiten Pfad zu konfigurieren. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für `/TheContactPage`Contact.cshtml*eine Route zur Seite*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seiten Modelle, der implementiert <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungs Punkte zum Konfigurieren von Seiten Modellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispiel-App eine `AddHeaderAttribute`-Klasse, bei der es sich um einen <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>handelt, der einen Antwortheader anwendet:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten unter dem angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert den angegebenen Filter für die Anwendung. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Da Aktionsfilter von Razor Pages ignoriert werden, hat die `EmptyFilter` keine Auswirkung, wenn der Pfad nicht `OtherPages/Page2`enthält.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert die angegebene Factory so, dass [Filter](xref:mvc/controllers/filters) auf alle Razor Pages angewendet werden.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden. Sie können jedoch andere Arten von MVC-Filtern verwenden: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seiten Filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der für Razor Pages gilt. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Verwenden Sie zum Angeben einer Seiten Route, zum Hinzufügen von Routen Segmenten oder zum Hinzufügen von Parametern zu einer Route die `@page` Direktive der Seite. Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routen Segmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: reservierte Routing Namen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Razor Pages Konventionen werden mithilfe der <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>-Erweiterungsmethode zum <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> der Dienst Auflistung in der `Startup`-Klasse hinzugefügt und konfiguriert. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

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

| Order            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird verarbeitet, bevor andere Routen verarbeitet werden. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird die Route für die Verarbeitung standardmäßig auf 0 (null) `Order`. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge der Route an. |

Die Routen Verarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen denselben `Order`haben, wird die spezifischere Route zuerst abgeglichen, gefolgt von weniger spezifischen Routen.
* Wenn Routen mit dem gleichen `Order` und der gleichen Anzahl von Parametern mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der Sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>hinzugefügt werden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Auftrags Verarbeitungsreihenfolge. Im Allgemeinen wählt das Routing die richtige Route mit URL-Übereinstimmung aus. Wenn Sie Routen `Order` Eigenschaften festlegen müssen, um Anforderungen ordnungsgemäß weiterzuleiten, ist das Routing Schema der APP wahrscheinlich verwirrend für Clients und anfällig für die Wartung. Versuchen Sie, das Routing Schema der APP zu vereinfachen. Die Beispiel-App erfordert eine explizite Routen Verarbeitungsreihenfolge, um verschiedene Routing Szenarien mithilfe einer einzelnen APP zu veranschaulichen. Sie sollten jedoch versuchen, die Vorgehensweise beim Festlegen von Routen `Order` in Produktions-apps zu vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routen Reihenfolge in den MVC-Themen finden [Sie unter Routing zu Controller Aktionen: Sortieren von Attribut Routen](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modell Konventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die für Razor Pages gelten.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routen Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des Seiten Routen Modells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Weiterleitungs Übereinstimmungs Verhalten in der Beispiel-App sichergestellt:

* Eine Routen Vorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Route der Kontaktseite hat eine Standard Reihenfolge `null` (`Order = 0`), sodass Sie vor der `{globalTemplate?}` Routen Vorlage übereinstimmt.
* Eine `{aboutTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine `{otherPagesTemplate?}` Routen Vorlage wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameter (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Razor Pages Optionen, wie z. b. das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn MVC der Dienst Sammlung in `Startup.ConfigureServices`hinzugefügt wird. In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer APP-Modell Konvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Instanzen, die während der Erstellung von Seiten-App-Modellen angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, um der Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>-Instanzen, die während der Erstellung des seitenhandlermodells angewendet werden, eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zu erstellen.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardrouten Modell Anbieter, der von abgeleitet wird <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die zum Bereitstellen von Erweiterungs Punkten zum Konfigurieren von Seiten Routen entworfen wurden.

### <a name="folder-route-model-convention"></a>Ordner Routen-Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die für alle Seiten im angegebenen Ordner eine Aktion auf dem <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn eine Seite im Ordner *pages/otherpages* mit einem Routen Parameterwert (z. b. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seiten Weiterleitungs Modell Konvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (zuvor im Thema `1`) Priorität für die erste Route-Daten Wert Position erhält, wenn ein einzelner Routen Wert bereitgestellt wird. Wenn auf `/About/RouteDataValue`die Seite "Info" mit einem Routen Parameterwert angefordert wird, wird "routedatavalue" in `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) geladen, weil die `Order`-Eigenschaft festgelegt wurde.

Legen Sie die `Order`, soweit möglich, nicht fest, was zu `Order = 0`führt. Verlassen Sie sich auf das Routing, um die richtige Route auszuwählen.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>, um eine Route zu einer Seite am angegebenen Seiten Pfad zu konfigurieren. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für `/TheContactPage`Contact.cshtml*eine Route zur Seite*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seiten Modelle, der implementiert <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungs Punkte zum Konfigurieren von Seiten Modellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispiel-App eine `AddHeaderAttribute`-Klasse, bei der es sich um einen <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>handelt, der einen Antwortheader anwendet:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten unter dem angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>, um eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zu erstellen und hinzuzufügen, die eine Aktion auf der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert den angegebenen Filter für die Anwendung. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Da Aktionsfilter von Razor Pages ignoriert werden, hat die `EmptyFilter` keine Auswirkung, wenn der Pfad nicht `OtherPages/Page2`enthält.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert die angegebene Factory so, dass [Filter](xref:mvc/controllers/filters) auf alle Razor Pages angewendet werden.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden. Sie können jedoch andere Arten von MVC-Filtern verwenden: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seiten Filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der für Razor Pages gilt. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
