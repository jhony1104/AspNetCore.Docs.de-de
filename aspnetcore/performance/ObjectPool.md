---
title: Wieder verwenden von Objekten mit Objectpool in ASP.net Core
author: rick-anderson
description: Tipps zum Steigern der Leistung in ASP.net Core-apps mit Objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 8244acb39a345875d80c5528a822de23f78b6e38
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403546"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Wieder verwenden von Objekten mit Objectpool in ASP.net Core

Von [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)und [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool>ist Teil der ASP.net Core-Infrastruktur, die unterstützt, dass eine Gruppe von Objekten für die Wiederverwendung im Speicher beibehalten wird, anstatt die Garbage Collection der Objekte zuzulassen.

Möglicherweise möchten Sie den Objekt Pool verwenden, wenn die verwalteten Objekte wie folgt lauten:

- Das Zuordnen/initialisieren ist aufwendig.
- Stellen Sie eine begrenzte Ressource dar.
- Wird vorhersag Bar und häufig verwendet.

Beispielsweise verwendet das ASP.net Core Framework den Objekt Pool an manchen Stellen, um Instanzen wiederzuverwenden <xref:System.Text.StringBuilder> . `StringBuilder`ordnet und verwaltet eigene Puffer zum Speichern von Zeichendaten. ASP.net Core werden regelmäßig verwendet `StringBuilder` , um Funktionen zu implementieren, und die Wiederverwendung bietet einen Leistungsvorteil.

Das Objekt Pooling verbessert nicht immer die Leistung:

- Wenn die Initialisierungs Kosten eines Objekts nicht hoch sind, ist es normalerweise langsamer, das Objekt aus dem Pool zu erhalten.
- Die Zuordnung von Objekten, die vom Pool verwaltet werden, wird erst aufgehoben, wenn die Zuordnung des Pools aufgehoben wird

Verwenden Sie das Objekt Pooling nur, nachdem Sie Leistungsdaten mithilfe realistischer Szenarien für Ihre APP oder Bibliothek gesammelt haben.

**Warnung: der `ObjectPool` implementiert nicht `IDisposable` . Es wird nicht empfohlen, es mit Typen zu verwenden, die eine Entsorgung benötigen.**

**Hinweis: die Anzahl der Objekte, die Sie zuordnen soll, wird von Objectpool nicht begrenzt, und die Anzahl der Objekte, die Sie beibehält, ist begrenzt.**

## <a name="concepts"></a>Konzepte

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>: die grundlegende Objekt Pool Abstraktion. Wird verwendet, um-Objekte zu erhalten und zurückzugeben.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implementieren Sie diese, um anzupassen, wie ein Objekt erstellt wird und wie es *zurückgesetzt* wird, wenn es an den Pool zurückgegeben wird. Dies kann an einen Objekt Pool übermittelt werden, den Sie direkt erstellen... Noch

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>fungiert als Factory zum Erstellen von Objekt Pools.
<!-- REview, there is no ObjectPoolProvider<T> -->

Der Objectpool kann in einer APP auf verschiedene Weise verwendet werden:

* Instanziieren eines Pools.
* Registrieren eines Pools in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) als-Instanz.
* Registrieren von `ObjectPoolProvider<>` in di und Verwendung als Factory.

## <a name="how-to-use-objectpool"></a>Verwenden von Objectpool

Ruft <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> auf, um ein-Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> das-Objekt zurückzugeben.  Es ist nicht erforderlich, jedes Objekt zurückzugeben. Wenn Sie kein Objekt zurückgeben, wird die Garbage Collection durchgeführt.

## <a name="objectpool-sample"></a>Objectpool-Beispiel

Der folgende Code

* Fügt `ObjectPoolProvider` dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) hinzu.
* Hiermit wird der di-Container hinzugefügt und konfiguriert `ObjectPool<StringBuilder>` .
* Fügt hinzu `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Der folgende Code implementiert`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
