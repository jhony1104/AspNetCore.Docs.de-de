---
title: Migrieren von ASP.NET Web-API in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von ASP.NET 4.x-Web-API ASP.NET Core MVC zu migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 74c7730a667ebc979241489733cdace149cacdf2
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555742"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrieren von ASP.NET Web-API in ASP.NET Core

Durch [Scott Addie](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)

Eine ASP.NET 4.x-Web-API ist ein HTTP-Dienst, der eine Breite Palette von Clients, einschließlich Browsern und mobilen Geräten erreicht. ASP.NET Core vereinheitlicht, ASP.NET 4.x MVC, und in ein einfacheres Programmiermodell, bekannt als ASP.NET Core MVC-Web-API-app-Modellen. Dieser Artikel beschreibt die erforderlichen Schritte zum Migrieren von ASP.NET 4.x-Web-API zu ASP.NET Core MVC.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Vorraussetzungen

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Überprüfen Sie die ASP.NET 4.x-Web-API-Projekts

Als Ausgangspunkt, in diesem Artikel wird die *ProductsApp* in erstelltes Projekt [erste Schritte mit ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). In diesem Projekt ist ein einfaches ASP.NET 4.x-Web-API-Projekt wie folgt konfiguriert werden.

In *"Global.asax.cs"* , erfolgt ein Aufruf zum `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

Die `WebApiConfig` Klasse befindet sich der *App_Start* Ordner und weist eine statische `Register` Methode:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Diese Klasse konfiguriert [attributrouting](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl sie nicht tatsächlich im Projekt verwendet wird. Außerdem konfiguriert die Routingtabelle, die von ASP.NET Web-API verwendet wird. In diesem Fall erwartet ASP.NET 4.x-Web-API-URLs, mit dem Format übereinstimmen `/api/{controller}/{id}`, mit `{id}` wird optional.

Die *ProductsApp* -Projekt enthält einen Controller. Der Controller erbt `ApiController` und enthält zwei Aktionen:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

Die `Product` verwendeten Modell `ProductsController` ist eine einfache Klasse:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

Die folgenden Abschnitte zeigen bei der Migration der Web-API-Projekts zu ASP.NET Core MVC.

## <a name="create-destination-project"></a>Zielprojekt erstellen

Führen Sie die folgenden Schritte aus, in Visual Studio:

* Wechseln Sie zu **Datei** > **neue** > **Projekt** > **anderen Projekttypen**  >  **Visual Studio-Projektmappen**. Wählen Sie **leere Projektmappe**, und nennen Sie die Projektmappe *WebAPIMigration*. Klicken Sie auf die **OK** Schaltfläche.
* Fügen Sie der vorhandenen *ProductsApp* Projekt der Projektmappe.
* Fügen Sie einen neuen **ASP.NET Core-Webanwendung** Projekt der Projektmappe. Wählen Sie die **.NET Core** Zielframework aus der Dropdownliste aus, und wählen Sie die **API** Projektvorlage. Nennen Sie das Projekt *ProductsCore*, und klicken Sie auf die **OK** Schaltfläche.

Die Projektmappe enthält jetzt zwei Projekte. In den folgenden Abschnitten wird erläutert, migrieren die *ProductsApp* Inhalt des Projekts, um die *ProductsCore* Projekt.

## <a name="migrate-configuration"></a>Migrieren der Konfiguration

ASP.NET Core verwendet nicht die *App_Start* Ordner oder die *"Global.asax"* -Datei, und die *"Web.config"* am Datei hinzugefügt wird, Zeitpunkt der Veröffentlichung. *"Startup.cs"* ist der Ersatz für *"Global.asax"* und befindet sich in das Stammverzeichnis des Projekts. Die `Startup` Klasse behandelt alle app-starttasks. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

In ASP.NET Core MVC attributrouting ist standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> unter "lautet" `Startup.Configure`. Die folgenden `UseMvc` aufrufen ersetzt die *ProductsApp* des Projekts *app_start/webapiconfig.cs* Datei:

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrieren von Modellen und Controllern

Kopieren Sie die *ProductApp* des Projekts-Controller und das Modell verwendet. Führen Sie folgende Schritte aus:

1. Kopie *Controllers/ProductsController.cs* aus dem ursprünglichen Projekt in das neue Projekt.
1. Kopieren Sie das gesamte *Modelle* Ordner aus dem ursprünglichen Projekt in das neue Projekt.
1. Ändern Sie die kopierten Dateien Namespaces entsprechend den neuen Projektnamen (*ProductsCore*). Anpassen der `using ProductsApp.Models;` -Anweisung in *ProductsController.cs* zu.

An diesem Punkt erstellen die app-Ergebnisse in eine Anzahl von Kompilierungsfehlern. Dieser Fehler tritt auf, da die folgenden Komponenten in ASP.NET Core nicht vorhanden sind:

* `ApiController`-Klasse
* `System.Web.Http`-Namespace
* `IHttpActionResult` Schnittstelle

Wie folgt vor, um die Fehler zu beheben:

1. Änderung `ApiController` zu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Hinzufügen `using Microsoft.AspNetCore.Mvc;` zum Auflösen der `ControllerBase` Verweis.
1. Löschen Sie `using System.Web.Http;`.
1. Ändern der `GetProduct` Rückgabetyp der Aktion von `IHttpActionResult` zu `ActionResult<Product>`.

Vereinfachen der `GetProduct` Aktion `return` -Anweisung wie folgt:

```csharp
return product;
```

## <a name="configure-routing"></a>Konfigurieren des Routings

Konfigurieren des Routings wie folgt:

1. Ergänzen Sie die `ProductsController` Klasse mit den folgenden Attributen:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Die obige [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfigurierte clientlaufzeitobjekt des Controllers Attribut-routing-Muster. Die [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut macht eine Voraussetzung für alle Aktionen in diesem Controller attributrouting.

    Attributrouting unterstützt das Token, z. B. `[controller]` und `[action]`. Zur Laufzeit wird jedes Token, mit dem Namen des Controllers oder der Aktion ersetzt, der das Attribut angewendet wurde. Die Token reduzieren die Anzahl der Magic-Zeichenfolgen im Projekt. Die Token stellen Sie sicher auch Routen mit den entsprechenden Controller synchronisiert bleiben, und Aktionen, wenn automatische umbenennen Refactorings gelten.
1. Festlegen des Projekts-Kompatibilitätsmodus in ASP.NET Core 2.2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Die zuvor vorgenommene Änderung:

    * Ist erforderlich, um Sie verwenden die `[ApiController]` Attribut auf der Controllerebene.
    * Verwendet möglicherweise wichtige Verhaltensweisen, die in ASP.NET Core 2.2 eingeführt.
1. Aktivieren von HTTP-Get-Anforderungen an die `ProductController` Aktionen:
    * Anwenden der [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) -Attribut auf die `GetAllProducts` Aktion.
    * Anwenden der `[HttpGet("{id}")]` -Attribut auf die `GetProduct` Aktion.

Nachdem Sie die vorangehenden Änderungen und das Entfernen nicht verwendeter `using` Anweisungen *ProductsController.cs* Datei sieht folgendermaßen aus:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Führen Sie die migrierte Projekt, und navigieren Sie zu `/api/products`. Eine vollständige Liste der drei Produkte angezeigt wird. Wechseln Sie zu `/api/products/1`. Das erste Produkt wird angezeigt.

## <a name="compatibility-shim"></a>Kompatibilitäts-Shims

Die [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek bietet eine kompatibilitätsshim zum Verschieben von ASP.NET 4.x-Web-API-Projekten in ASP.NET Core. Der Kompatibilitäts-Shims erweitert ASP.NET Core, um eine Reihe von Konventionen von ASP.NET 4.x-Web-API 2 zu unterstützen. Das Beispiel, das zuvor in diesem Dokument portiert ist einfach genug, dass der Kompatibilitäts-Shims nicht erforderlich war. Für größere Projekte kann mithilfe der Kompatibilitäts-Shims für vorübergehend die API-Lücke, zwischen ASP.NET Core und ASP.NET 4.x-Web-API 2 nützlich.

Die Web-API-kompatibilitätsshim soll als vorübergehende Maßnahme zur Unterstützung der Migration großer ASP.NET 4.x-Web-API-Projekten in ASP.NET Core verwendet werden. Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.NET Core-Muster zu verwenden, statt die standardremotedatenbank von der Kompatibilitäts-Shims.

Kompatibilitätsfeatures in enthaltenen `Microsoft.AspNetCore.Mvc.WebApiCompatShim` enthalten:

* Fügt eine `ApiController` geben, damit Controllern Basistypen nicht aktualisiert werden müssen.
* Ermöglicht die modellbindung für die Web-API-Format. ASP.NET Core MVC-Modell Bindung funktioniert ähnlich, die von ASP.NET 4.x MVC 5, in der Standardeinstellung. Der Shim-kompatibilitätsänderungen modellbindung ähnelt eher der ASP.NET 4.x-Web-API 2-Bindung modellkonventionen sein. Komplexe Typen werden beispielsweise automatisch aus dem Anforderungstext gebunden.
* Modellbindung erweitert, sodass Controlleraktionen Parameter des Typs nutzen können `HttpRequestMessage`.
* Fügt der Nachrichtenformatierungsprogramme zulassen von Aktionen zum Zurückgeben von Ergebnissen vom Typ `HttpResponseMessage`.
* Fügt zusätzliche Response-Methoden, die Web-API 2-Aktionen verwendet haben können, um Antworten zu verarbeiten:
  * `HttpResponseMessage` Generatoren:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Ergebnis-Aktionsmethoden:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Fügt eine Instanz der `IContentNegotiator` an die app die Container für Abhängigkeitsinjektion (DI) und die Aushandlung bezogenen Inhaltstypen aus zur Verfügung gestellt [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Beispiele für solche Typen `DefaultContentNegotiator` und `MediaTypeFormatter`.

So verwenden Sie die Kompatibilitäts-Shims

1. Installieren Sie die [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet-Paket.
1. Registrieren der Kompatibilitäts-Shims für die Dienste mit der app-DI-Container durch Aufrufen von `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.
1. Definieren Sie die Web-API-spezifische Routen mit `MapWebApiRoute` auf die `IRouteBuilder` der App `IApplicationBuilder.UseMvc` aufrufen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
