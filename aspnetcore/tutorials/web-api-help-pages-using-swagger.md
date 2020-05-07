---
title: ASP.NET Core-Web-API-Hilfeseiten mit Swagger/OpenAPI
author: RicoSuter
description: Dieses Tutorial enthält eine exemplarische Vorgehensweise für das Hinzufügen von Swagger, um Dokumentationen und Hilfeseiten für eine Web-API-App zu generieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: bde38fcbc11ef36c42523acb182fc62a934821c3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774521"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core-Web-API-Hilfeseiten mit Swagger/OpenAPI

Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://blog.rsuter.com/)

Wenn eine Web-API verwendet wird, fällt es dem Entwickler oft nicht leicht, die zahlreichen Methoden zu verstehen. [Swagger](https://swagger.io/), auch als [OpenAPI](https://www.openapis.org/) bekannt, löst das Problem, das das Generieren von nützlichen Dokumentationen und Hilfeseiten für Web-APIs darstellen kann. Das Programm bietet Vorteile wie die interaktive Dokumentation, die Generierung von Client SDKs und die Erkennbarkeit von APIs.

In diesem Artikel werden die Swagger-Implementierungen [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) und [NSwag](https://github.com/RicoSuter/NSwag) von .NET veranschaulicht:

* **Swashbuckle.AspNetCore** ist ein Open Source-Projekt zum Generieren von Swagger-Dokumenten für Web-APIs von ASP.NET Core.

* **NSwag** ist ein weiteres Open-Source-Projekt zum Generieren von Swagger-Dokumenten und Integrieren von [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) oder [ReDoc](https://github.com/Rebilly/ReDoc) in ASP.NET Core-Web-APIs. Außerdem bietet NSwag Verfahren, um C#- und TypeScript-Clientcode für Ihre API zu generieren.

## <a name="what-is-swagger--openapi"></a>Was ist Swagger bzw. OpenAPI?

Bei Swagger handelt es sich um eine sprachunabhängige Spezifikation für das Beschreiben von [REST-APIs](https://en.wikipedia.org/wiki/Representational_state_transfer). Das Swagger-Projekt wurde an die [OpenAPI Initiative](https://www.openapis.org/) übergeben. Dort wird es nun als „OpenAPI“ bezeichnet. Beide Namen werden austauschbar verwendet, jedoch wird „OpenAPI“ bevorzugt. Dadurch können Computer und Benutzer die Funktionen eines Diensts ohne direkten Zugriff auf die Implementierung (Quellcode, Netzwerkzugriff, Dokumentation) nachvollziehen. Ein Ziel besteht im Minimieren des Arbeitsaufwands, der zum Verbinden von getrennten Diensten erforderlich ist. Ein weiteres Ziel besteht darin, den Zeitaufwand zu verringern, der für die genaue Dokumentation eines Diensts erforderlich ist.

## <a name="swagger-specification-swaggerjson"></a>Spezifikation von Swagger (swagger.json)

Im Zentrum des Swagger-Flows steht die Swagger-Spezifikation. Diese ist standardmäßig ein Dokument namens *swagger.json*. Dieses wird je nachdem, welchen Dienst Sie verwenden, von der Swagger-Toolkette (oder von einer Drittanbieterimplementierung davon) generiert. Es beschreibt die Funktionen Ihrer API und wie auf diese mit HTTP zugegriffen werden kann. Es führt die Swagger-Benutzeroberfläche aus und wird von der Toolkette verwendet, um die Ermittlung und die Generierung von Clientcode zu aktivieren. Hier finden Sie ein Beispiel der Swagger-Spezifikation, das aus Gründen der Übersichtlichkeit reduziert wurde:

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Swagger-Benutzeroberfläche

Die [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) stellt eine webbasierte Benutzeroberfläche bereit, die Informationen über den Dienst enthält und die generierte Swagger-Spezifikation verwendet. Swashbuckle und NSwag enthalten eine eingebettete Version der Swagger-Benutzeroberfläche, sodass diese in Ihrer ASP.NET Core-App mithilfe eines Registrierungsaufrufs für die Middleware gehostet werden kann. Die Webbenutzeroberfläche sieht folgendermaßen aus:

![Swagger-Benutzeroberfläche](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Jede öffentliche Aktionsmethode in Ihren Controllern kann über die Benutzeroberfläche getestet werden. Klicken Sie auf einen Methodennamen, um den Abschnitt zu erweitern. Fügen Sie die erforderlichen Parameter hinzu, und klicken Sie auf **Probieren Sie es aus!** .

![Beispiel für einen Swagger-GET-Test](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Die für den Screenshot verwendete Version der Swagger-Benutzeroberfläche ist Version 2. Ein Beispiel für Version 3 finden Sie unter [Pet store example (Beispiel für eine Tierhandlung)](https://petstore.swagger.io/).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Erste Schritte mit NSwag](xref:tutorials/get-started-with-nswag)
