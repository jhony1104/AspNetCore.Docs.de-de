---
title: Objekt-Wiederverwendung mit Objektpool in ASP.NET Core
author: rick-anderson
description: Tipps zum Verbessern der Leistung in ASP.NET Core-apps, die mit der Objektpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815523"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Objekt-Wiederverwendung mit Objektpool in ASP.NET Core

Durch [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), und [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> ist Teil der ASP.NET Core-Infrastruktur, die unterstützt wird, halten eine Gruppe von Objekten im Arbeitsspeicher für die Wiederverwendung, anstatt können die Objekte in die Garbage erfasst.

Möglicherweise möchten den-Objektpool verwenden, wenn die Objekte, die verwaltet werden:

- Teuer zuordnen/initialisiert werden.
- Stellen Sie eine begrenzte Ressource dar.
- Zuverlässig und häufig verwendet.

Das ASP.NET Core-Framework verwendet beispielsweise des Objektpools an einigen Stellen wiederverwenden <xref:System.Text.StringBuilder> Instanzen. `StringBuilder` Ordnet ein, und verwaltet seine eigenen Puffer zum Speichern von Zeichendaten enthalten sind. ASP.NET Core verwendet, regelmäßig `StringBuilder` zum Implementieren dieses features, und führt zu einer Leistungssteigerung wiederverwendet werden.

Objektpooling nicht immer die Leistung verbessert:

- Es sei denn, die Kosten der Initialisierung eines Objekts hoch ist, ist es in der Regel langsamer, um das Objekt aus dem Pool abzurufen.
- Durch den Pool verwalteten Objekte nicht aufgehoben, bis der Pool aufgehoben ist.

Verwenden Sie die Objektpooling nur nach dem Sammeln von Leistungsdaten mithilfe von realistischen Szenarien für Ihre app oder Bibliothek.

**WARNUNG: Die `ObjectPool` implementiert keine `IDisposable`. Verwenden es mit Typen, die Freigabe benötigen empfohlen nicht.**

**HINWEIS: Der Objektpool keine Begrenzung der Anzahl der Objekte, die er zuordnet, es legt einen Grenzwert für die Anzahl der Objekte, die es beibehalten wird.**

## <a name="concepts"></a>Konzepte

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -die Pool-Abstraktion Basisobjekt. Zum Abrufen und Objekte zurückgegeben.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> – Implementieren Sie diese Option, um anzupassen, wie ein Objekt erstellt wird und wie es *zurücksetzen* Wenn an den Pool zurückgegeben. Dies kann in einem Pool-Objekt übergeben werden, die Sie direkt zu erstellen... oder

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> fungiert als Factory für die Erstellung von Objektpools.
<!-- REview, there is no ObjectPoolProvider<T> -->

Der Objektpool kann in einer app auf verschiedene Weise verwendet werden:

* Instanziieren einen Pool an.
* Registrieren einen Pool in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (DI) als eine Instanz.
* Registrieren der `ObjectPoolProvider<>` in DI, und verwenden ihn als Factory.

## <a name="how-to-use-objectpool"></a>Gewusst wie: Verwenden der Objektpool

Rufen Sie <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> um ein Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> das Objekt zurück.  Es ist nicht erforderlich, dass Sie jedes Objekt zurückgeben. Wenn Sie ein Objekt zurückgeben keine, werden Garbage Collection bereinigt.

## <a name="objectpool-sample"></a>Objektpool-Beispiel

Der folgende Code

* Fügt `ObjectPoolProvider` auf die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (DI)-Container.
* Hinzugefügt und konfiguriert `ObjectPool<StringBuilder>` zu DI-Container.
* Fügt der `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Der folgende Code implementiert `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
