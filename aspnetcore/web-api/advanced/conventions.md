---
title: Verwenden von Web-API-Konventionen
author: pranavkm
description: Informationen zu Web-API-Konventionen in ASP.NET Core
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 481e3810f1e1aca40e0ee1ce3da6c67dc9d841f4
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425106"
---
# <a name="use-web-api-conventions"></a>Verwenden von Web-API-Konventionen

Von [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2.2 und höher umfasst eine Möglichkeit, allgemeine [API-Dokumentation](xref:tutorials/web-api-help-pages-using-swagger) zu extrahieren und diese auf mehrere Aktionen, Controller sowie alle Controller in einer Assembly anzuwenden. Web-API-Konventionen können verwendet werden, damit keine einzelnen Aktionen mit [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) versehen werden müssen.

Eine Konvention ermöglicht Ihnen Folgendes:

* Definieren der gebräuchlichsten Rückgabetypen und Statuscodes, die von einem bestimmten Typ von Aktion zurückgegeben werden
* Erkennen von Aktionen, die vom definierten Standard abweichen

ASP.NET Core MVC 2.2 und höher umfasst eine Reihe von Standardkonventionen in `Microsoft.AspNetCore.Mvc.DefaultApiConventions`. Die Konventionen basieren auf dem Controller (*ValuesController.cs*), der in der ASP.NET Core-Projektvorlage **API** bereitgestellt wird. Wenn Ihre Aktionen den Mustern in der Vorlage entsprechen, sollten die Standardkonventionen für Sie ausreichend sein. Wenn die Standardkonventionen Ihre Anforderungen nicht erfüllen, lesen Sie [Erstellen von Web-API-Konventionen](#create-web-api-conventions).

<xref:Microsoft.AspNetCore.Mvc.ApiExplorer> erkennt zur Laufzeit Konventionen. `ApiExplorer` ist die Abstraktion von MVC, über die mit [OpenAPI](https://www.openapis.org/)-Dokumentgeneratoren kommuniziert wird (OpenAPI ist auch als Swagger bekannt). Die Attribute aus der angewendeten Konvention werden einer Aktion zugeordnet und zur OpenAPI-Dokumentation der Aktion hinzugefügt. Auch [API-Analysetools](xref:web-api/advanced/analyzers) erkennen Konventionen. Wenn Ihre Aktion keiner Konvention folgt (also z. B. einen Statuscode zurückgibt, der nicht durch die angewendete Konvention dokumentiert ist), werden Sie mit einer Warnung aufgefordert, den Statuscode zu dokumentieren.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Anwenden von Web-API-Konventionen

Konventionen werden nicht miteinander kombiniert. Jede Aktion kann genau einer Konvention zugeordnet werden. Speziellere Konventionen haben Vorrang vor weniger speziellen Konventionen. Die Auswahl ist nicht deterministisch, wenn mindestens zwei Konventionen mit gleicher Priorität für eine Aktion gelten. Sie können eine der folgenden Optionen auswählen, um angefangen bei der genausten Konvention bis hin zur ungenausten eine Konvention auf eine Aktion anzuwenden:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Gilt für einzelne Aktionen und gibt den jeweiligen passenden Konventionstyp und die Konventionsmethode an.

    Im folgenden Beispiel wird die `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`-Konventionsmethode des Standardkonventionstyps auf die `Update`-Aktion angewendet:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    Die `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`-Konventionsmethode wendet die folgenden Attribute auf die Aktion an:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    [ProducesResponseType(400)]
    ```

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, auf einen Controller angewendet &mdash; Wendet den angegebenen Konventionstyp auf alle Controlleraktionen an. Eine Konventionsmethode ist mit Hinweisen versehen, die die Aktionen bestimmen, für die die Konventionsmethode gilt. Weitere Informationen zu Hinweisen finden Sie unter [Erstellen von Web-API-Konventionen](#create-web-api-conventions)).

    Im folgenden Beispiel werden alle standardmäßigen Konventionen auf alle Aktionen in *ContactsConventionController* angewendet:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, auf eine Assembly angewendet &mdash; Wendet den angegebenen Konventionstyp auf alle Controller in der aktuellen Assembly an. Es empfiehlt sich, Attribute auf Assemblyebene auf die `Startup`-Klasse anzuwenden.

    Im folgenden Beispiel werden alle standardmäßigen Konventionen auf alle Controller in der Assembly angewendet:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Erstellen von Web-API-Konventionen

Wenn die Standard-API-Konventionen Ihre Anforderungen nicht erfüllen, erstellen Sie eigene Konventionen. Eine Konvention ist:

* Ein statischer Typ mit Methoden.
* Geeignet, [Antworttypen](#response-types) und [Benennungsanforderungen](#naming-requirements) für Aktionen zu definieren.

### <a name="response-types"></a>Antworttypen

Diese Methoden werden mit den Attributen `[ProducesResponseType]` oder `[ProducesDefaultResponseType]` versehen. Beispiel:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

Sind keine spezielleren Metadatenattribute vorhanden, erzwingt ein Anwenden dieser Konvention auf eine Assembly, dass:

* Die Konventionsmethode für jede Aktion namens `Find` gilt.
* Ein Parameter namens `id` in der `Find`-Aktion vorhanden ist.

### <a name="naming-requirements"></a>Benennungsanforderungen

Die Attribute `[ApiConventionNameMatch]` und `[ApiConventionTypeMatch]` können auf die Konventionsmethode angewendet werden, die die Aktionen bestimmt, für die sie gelten. Beispiel:

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

Im vorherigen Beispiel:

* Die auf die Methode angewendete Option `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` gibt an, dass die Konvention für jede Aktion gilt, die das Präfix „Find“ hat. Beispiele für übereinstimmende Aktionen sind `Find`, `FindPet` und `FindById`.
* Die auf den Parameter angewendete Option `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` gibt an, dass die Konvention für jede Methode gilt, die genau einen Parameter hat, der mit dem Suffix „id“ endet. Beispiele sind Parameter wie `id` und `petId`. Vergleichbar kann `ApiConventionTypeMatch` auf Typen angewendet, um den Parametertyp einzuschränken. Ein `params[]`-Argument gibt verbleibende Parameter an, die nicht explizit abgeglichen werden müssen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
