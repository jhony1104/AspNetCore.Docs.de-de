---
title: Migrieren von ASP.net-Web-API zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von der ASP.NET 4. x-Web-API zu ASP.net Core MVC migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: c68cf83f427f53b110075168c6d5e4d021808782
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881149"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrieren von ASP.net-Web-API zu ASP.net Core

Von [Scott Adder](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)

Eine ASP.NET 4. x-Web-API ist ein HTTP-Dienst, der eine breite Palette von Clients, einschließlich Browser und mobiler Geräte, erreicht. ASP.net Core vereinheitlicht die MVC-und Web-API-APP-Modelle von ASP.NET 4. x in ein einfacheres Programmiermodell, das als ASP.net Core MVC bezeichnet wird. In diesem Artikel werden die erforderlichen Schritte zum Migrieren von der ASP.NET 4. x-Web-API zu ASP.net Core MVC erläutert.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Voraussetzungen

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x-Web-API-Projekt überprüfen

Als Ausgangspunkt wird in diesem Artikel das *productapp* -Projekt verwendet, das in erste Schritte [mit ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt wurde. In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.

In *Global.asax.cs*wird ein-`WebApiConfig.Register`aufgerufen:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

Die `WebApiConfig`-Klasse befindet sich im Ordner *App_Start* und verfügt über eine statische `Register`-Methode:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Diese Klasse konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl Sie nicht tatsächlich im Projekt verwendet wird. Außerdem wird die Routing Tabelle konfiguriert, die von ASP.net-Web-API verwendet wird. In diesem Fall erwartet die ASP.NET 4. x-Web-API, dass URLs dem Format `/api/{controller}/{id}`entsprechen, wobei `{id}` optional ist.

Das Projekt *producungapp* enthält einen Controller. Der Controller erbt von `ApiController` und enthält zwei Aktionen:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

Das `Product` Modell, das von `ProductsController` verwendet wird, ist eine einfache Klasse:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.

## <a name="create-destination-project"></a>Ziel Projekt erstellen

Führen Sie in Visual Studio die folgenden Schritte aus:

* Wechseln Sie zu **Datei** > **Neues** > **Projekt** > **andere Projekttypen** > **Visual Studio**-Projektmappen. Wählen Sie **leere**Projekt Mappe aus, und nennen Sie die Projekt Mappe *webapimigration*. Klicken Sie auf die Schaltfläche **OK** .
* Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.
* Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu. Wählen Sie in der Dropdown Liste das Ziel Framework **.net Core** aus, und wählen Sie die Vorlage **API** -Projekt aus. Nennen Sie das Projekt *productscore*, und klicken Sie auf die Schaltfläche **OK** .

Die Projekt Mappe enthält jetzt zwei Projekte. In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.

## <a name="migrate-configuration"></a>Konfiguration migrieren

ASP.net Core verwendet nicht den Ordner *App_Start* oder die Datei *Global. asax* , und die Datei *Web. config* wird zum Zeitpunkt der Veröffentlichung hinzugefügt. *Startup.cs* ist der Ersatz für *Global. asax* und befindet sich im Stammverzeichnis des Projekts. Die `Startup`-Klasse behandelt alle App-Start Tasks. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

In ASP.net Core MVC ist das Attribut Routing standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> in `Startup.Configure`aufgerufen wird. Der folgende `UseMvc`-Befehl ersetzt die *App_Start/webapiconfig.cs* -Datei des Projekts " *producungapp* ":

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrieren von Modellen und Controllern

Kopieren Sie den Controller des *productapp* -Projekts und das verwendete Modell. Führen Sie folgende Schritte aus:

1. Kopieren Sie " *Controllers/productioncontroller. cs* " aus dem ursprünglichen Projekt in das neue Projekt.
1. Kopieren Sie den gesamten Ordner " *Models* " aus dem ursprünglichen Projekt in das neue Projekt.
1. Ändern Sie die Namespaces der kopierten Dateien so, dass Sie mit dem neuen Projektnamen (*productscore*) identisch sind. Passen Sie auch die `using ProductsApp.Models;` Anweisung in *ProductsController.cs* an.

Zu diesem Zeitpunkt führt das Entwickeln der APP zu einer Reihe von Kompilierungs Fehlern. Die Fehler treten auf, weil die folgenden Komponenten nicht in ASP.net Core vorhanden sind:

* `ApiController`-Klasse
* `System.Web.Http`-Namespace
* `IHttpActionResult`-Schnittstelle

Beheben Sie die Fehler wie folgt:

1. Änderung `ApiController` zu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Fügen Sie `using Microsoft.AspNetCore.Mvc;` hinzu, um den `ControllerBase` Verweis aufzulösen.
1. Löschen Sie `using System.Web.Http;`.
1. Ändern Sie den Rückgabetyp der `GetProduct` Aktion von `IHttpActionResult` in `ActionResult<Product>`.

Vereinfachen Sie die `return`-Anweisung der `GetProduct` Aktion wie folgt:

```csharp
return product;
```

## <a name="configure-routing"></a>Konfigurieren des Routings

Konfigurieren Sie das Routing wie folgt:

1. Markieren Sie die `ProductsController`-Klasse mit den folgenden Attributen:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers. Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) -Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.

    Das Attribut Routing unterstützt Token, z. b. `[controller]` und `[action]`. Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde. Die Token reduzieren die Anzahl der magischen Zeichen folgen im Projekt. Die Token stellen außerdem sicher, dass Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn die refactoren für automatisches umbenennen angewendet werden.
1. Legen Sie den Kompatibilitätsmodus des Projekts auf ASP.net Core 2,2 fest:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Die vorherige Änderung:

    * Ist erforderlich, um das `[ApiController]`-Attribut auf Controller Ebene zu verwenden.
    * Gibt an, dass potenziell wichtige Verhaltensweisen in ASP.net Core 2,2 eingeführt werden.
1. HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:
    * Wenden Sie das [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) -Attribut auf die `GetAllProducts` Aktion an.
    * Wenden Sie das `[HttpGet("{id}")]`-Attribut auf die `GetProduct` Aktion an.

Nach den vorangehenden Änderungen und dem Entfernen nicht verwendeter `using`-Anweisungen sieht die *ProductsController.cs* -Datei wie folgt aus:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products`. Es wird eine vollständige Liste der drei Produkte angezeigt. Wechseln Sie zu `/api/products/1`. Das erste Produkt wird angezeigt.

## <a name="compatibility-shim"></a>Kompatibilitäts-Shim

Die [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek stellt einen Kompatibilitäts-Shim bereit, um ASP.NET 4. x-Web-API-Projekte in ASP.net Core zu verschieben. Der Kompatibilitäts-Shim erweitert ASP.net Core, um eine Reihe von Konventionen von ASP.NET 4. x Web-API 2 zu unterstützen. Das zuvor in diesem Dokument portierte Beispiel ist so einfach, dass das Kompatibilitäts-Shim unnötig ist. Bei größeren Projekten kann die Verwendung des Kompatibilitäts-Shims nützlich sein, um die API-Lücke zwischen ASP.net Core und ASP.NET 4. x-Web-API 2 vorübergehend zu überbrücken.

Das Web-API-Kompatibilitäts-Shim ist als temporäre Maßnahme zur Unterstützung der Migration von großen ASP.NET 4. x-Web-API-Projekten zu ASP.net Core gedacht. Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.net Core Muster zu verwenden, anstatt sich auf den Kompatibilitäts-Shim zu verlassen.

Zu den in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` enthaltenen Kompatibilitäts Features gehören:

* Fügt einen `ApiController` Typ hinzu, sodass die Basis Typen der Controller nicht aktualisiert werden müssen.
* Ermöglicht die Modell Bindung im Web-API-Stil. ASP.net Core die MVC-Modell Bindung ähnlich der von ASP.NET 4. x MVC 5 standardmäßig. Mit dem Kompatibilitäts-Shim wird die Modell Bindung so geändert, dass Sie den Modell Bindungs Konventionen von ASP.NET 4. x Web API 2 ähnelt. Komplexe Typen werden z. b. automatisch vom Anforderungs Text gebunden.
* Erweitert die Modell Bindung, sodass Controller Aktionen Parameter vom Typ `HttpRequestMessage`annehmen können.
* Fügt nachrichtenformatierer hinzu, sodass Aktionen Ergebnisse vom Typ `HttpResponseMessage`zurückgeben können.
* Fügt zusätzliche Antwort Methoden hinzu, die von Web-API 2-Aktionen verwendet werden können, um Antworten zu erfüllen:
  * `HttpResponseMessage` Generatoren:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Aktions Ergebnis Methoden:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Fügt dem Container für die Abhängigkeitsinjektion der App eine Instanz von `IContentNegotiator` hinzu und stellt die inhaltsaushandlungs bezogenen Typen von [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)zur Verfügung. Beispiele für solche Typen sind `DefaultContentNegotiator` und `MediaTypeFormatter`.

So verwenden Sie den Kompatibilitäts-Shim:

1. Installieren Sie das nuget-Paket [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registrieren Sie die Dienste des Kompatibilitäts-Shims mit dem di-Container der APP, indem Sie `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`aufrufen.
1. Definieren Sie Web-API-spezifische Routen mithilfe von `MapWebApiRoute` auf der `IRouteBuilder` im `IApplicationBuilder.UseMvc`-Befehl der app.

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
