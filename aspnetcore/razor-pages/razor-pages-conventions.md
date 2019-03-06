---
title: 'Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core'
author: guardrex
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/27/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 5cfcae5cffd5d9484ca64c3885b838ae0a2b4a0d
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346514"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor-Seiten: Routen- und App-Konventionen in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Sie in Apps für Razor-Seiten mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Verwenden Sie zum Angeben einer seitenroute, Hinzufügen von routensegmenten oder Hinzufügen von Parametern für eine Route, die der Seite `@page` Richtlinie. Weitere Informationen finden Sie unter [benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es sind reservierte Wörter, die als routensegmenten oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: Reservierte routing Namen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Konventionen für Razor-Seiten werden hinzugefügt und konfiguriert die <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> auf die Sammlung von Diensten in der `Startup` Klasse. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>Reihenfolge

Routen geben eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung (Route übereinstimmend).

| Auftrag            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird verarbeitet, bevor andere Routen verarbeitet werden. |
| 0                | Reihenfolge nicht angegeben ist (Standardwert). Kein Zuweisen von `Order` (`Order = null`) wird standardmäßig die Route `Order` auf 0 (null) für die Verarbeitung. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge für die Route an. |

Die Verarbeitung Route wird gemäß der Konvention hergestellt:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn die Routen haben die gleiche `Order`, wird die bestimmte Route zuerst gefolgt von weniger spezifische Routen abgeglichen wird.
* Wenn Routen mit dem gleichen `Order` und die gleiche Anzahl von Parametern mit eine Anforderungs-URL übereinstimmen, Routen werden in der Reihenfolge verarbeitet, die hinzugefügt werden die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Vermeiden Sie wenn möglich, je nach einem etablierten Route Verarbeitungsreihenfolge. Im Allgemeinen wird beim routing mit URL-Zuordnung die richtige Route ausgewählt. Wenn Sie, Route festlegen müssen `Order` Eigenschaften zum Weiterleiten von Anforderungen ordnungsgemäß routing der app-Schema ist wahrscheinlich für Clients verwirrend und fehleranfälliger zu verwalten. Suchen Sie zur Vereinfachung der app-routing-Schema. Die Beispiel-app erfordert eine explizite Route Verarbeitungsreihenfolge mehrere Routingszenarien verwenden eine einzelne app veranschaulicht. Allerdings sollten Sie versuchen, vermeiden Sie die Methode der Einstellung Route `Order` in Produktions-apps.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Reihenfolge der MVC-Themen finden Sie unter [Routing zu Controlleraktionen: Ordnen der attributrouten](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzuzufügende [modellieren Konventionen](xref:mvc/controllers/application-model#conventions) gilt für Razor-Seiten.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer routenmodellkonvention zu allen Seiten

Verwendung <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zur Auflistung der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> model Konstruktion-Instanzen, die während der seitenroute angewendet werden.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird die folgende Route Vergleichsverhalten in der Beispiel-app:

* Eine routenvorlage für `TheContactPage/{text?}` wird später im Thema hinzugefügt. Die Seite "Kontakt"-Route hat eine Standardreihenfolge der `null` (`Order = 0`), bevor Sie entsprechend der `{globalTemplate?}` routenvorlage.
* Ein `{aboutTemplate?}` routenvorlage wird später im Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Ein `{otherPagesTemplate?}` routenvorlage wird später im Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite der *Seiten/OtherPages* Ordner mit einem Routenparameter angefordert wird (z. B. `/OtherPages/Page1/RouteDataValue`), wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.

Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`. Wählen Sie die richtige Route routing nutzen.

Razor-Seiten-Optionen, z. B. das Hinzufügen <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn die Sammlung von Diensten in MVC hinzugefügt wird `Startup.ConfigureServices`. In der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer app-modellkonvention zu allen Seiten

Verwenden <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zur Auflistung der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> -Instanzen, die während der angewendet werden Seite Modellentwicklung app.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer Ereignishandler-modellkonvention zu allen Seiten

Verwendung <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> auf die Auflistung von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> -Instanzen, die während der angewendet werden Seite Modellentwicklung Handler.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardwert für routenmodelle, die von abgeleitet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von seitenrouten bereitzustellen.

### <a name="folder-route-model-convention"></a>Ordnerroutenmodellkonvention

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für alle Seiten in dem angegebenen Ordner.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Legen Sie weiter oben in das Thema `1`) Bezug auf die erste Routendaten Position Wert, wenn ein einziger routenwert angegeben wird. Wenn eine Seite in der *Seiten/OtherPages* Ordner mit einem routenparameterwert angefordert wird (z. B. `/OtherPages/Page1/RouteDataValue`), wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.

Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`. Wählen Sie die richtige Route routing nutzen.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seitenroutenmodellkonvention

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}` (Legen Sie weiter oben in das Thema `1`) Bezug auf die erste Routendaten Position Wert, wenn ein einziger routenwert angegeben wird. Wenn die Seite "Info" angefordert wird, mit dem Route-Parameterwert an `/About/RouteDataValue`, wird "RouteDataValue" in den geladen `RouteData.Values["globalTemplate"]` (`Order = 1`) und nicht `RouteData.Values["aboutTemplate"]` (`Order = 2`) aufgrund der Einstellung der `Order` Eigenschaft.

Wo immer dies möglich ist, legen Sie nicht die `Order`, was dazu führt `Order = 0`. Wählen Sie die richtige Route routing nutzen.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden Sie einen Transformator Parameter zum Anpassen von seitenrouten

Von ASP.NET Core generierte seitenrouten können mit der ein Transformator Parameter angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `PageRouteTransformerConvention` seitenroutenmodellkonvention gilt einen Transformator Parameter für die namenssegmente Ordner- und von automatisch generierten Seitenprofilklasse Routen in einer app. Z. B. die Razor-Seiten, die auf Dateien */Pages/SubscriptionManagement/ViewAll.cshtml* müsste der Route aus umgeschrieben `/SubscriptionManagement/ViewAll` zu `/subscription-management/view-all`.

`PageRouteTransformerConvention` nur transformiert die automatisch generierten Segmenten einer seitenroute, die von den Razor-Seiten-Ordner und Dateinamen stammen. Keine Transformation routensegmenten hinzugefügt, mit der `@page` Richtlinie. Die Konvention nicht transformiert und außerdem hinzugefügten Routen <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

Die `PageRouteTransformerConvention` registriert, ist als eine Option in `Startup.ConfigureServices`:

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

Verwendung <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> so konfigurieren Sie eine Route zu einer Seite am angegebenen Seitenpfad. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:

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

Der Standardanbieter für seitenmodelle, die implementiert <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von seitenmodellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

Für die Beispiele in diesem Abschnitt, die Beispiel-app verwendet eine `AddHeaderAttribute` Klasse, die eine <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, die für einen Antwortheader gilt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwendung <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , die auf eine Aktion aufruft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> Instanzen für alle Seiten in den angegebenen Ordner.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , das eine Aktion aufruft, auf die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert den angegebenen Filter anwenden. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Wenn der Pfad `OtherPages/Page2` nicht enthält, hat der `EmptyFilter`, wie vorgesehen, keine Funktion, da Aktionsfilter von Razor Pages ignoriert werden.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert die angegebene Zuordnungsinstanz so gelten [Filter](xref:mvc/controllers/filters) für alle Razor-Seiten.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da diese Handlermethoden verwenden. Andere Arten von MVC-Filter sind verfügbar, Sie verwenden: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters), und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seitenfilter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, die auf Razor Pages anwendbar. Weitere Informationen finden Sie unter [Filtermethoden für Razor-Seiten](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Autorisierungskonventionen für Razor Pages](xref:security/authorization/razor-pages-authorization)
