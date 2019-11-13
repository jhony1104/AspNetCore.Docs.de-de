---
title: Beginnen Sie mit den Datenschutz-APIs in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die ASP.net Core-Datenschutz-APIs verwenden, um Daten in einer APP zu schützen und zu schützen.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963862"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Beginnen Sie mit den Datenschutz-APIs in ASP.net Core

<a name="security-data-protection-getting-started"></a>

Am einfachsten besteht der Schutz von Daten aus den folgenden Schritten:

1. Erstellen Sie eine Daten Schutzvorrichtung von einem Datenschutzanbieter.

2. Nennen Sie die `Protect`-Methode mit den Daten, die Sie schützen möchten.

3. Nennen Sie die `Unprotect`-Methode mit den Daten, die Sie wieder in Klartext umwandeln möchten.

Bei den meisten Frameworks und App-Modellen, wie ASP.net Core oder SignalR, wird das Datenschutzsystem bereits konfiguriert und einem Dienst Container hinzugefügt, auf den Sie über die Abhängigkeitsinjektion zugreifen. Das folgende Beispiel veranschaulicht das Konfigurieren eines Dienst Containers für die Abhängigkeitsinjektion und das Registrieren des Datenschutz Stapels, das Empfangen des Datenschutz Anbieters über di, das Erstellen einer Schutzvorrichtung und das Schützen des Schutzes von Daten.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Wenn Sie eine Schutzvorrichtung erstellen, müssen Sie eine oder mehrere [Zweck](xref:security/data-protection/consumer-apis/purpose-strings)Zeichenfolgen angeben. Eine Zweck Zeichenfolge ermöglicht die Isolation zwischen Consumern. Beispielsweise wäre eine Schutzvorrichtung, die mit einer Zweck Zeichenfolge "grün" erstellt wurde, nicht in der Lage, die Daten zu schützen, die von einer Schutzvorrichtung mit dem Zweck "Lila" bereitgestellt werden

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher. Wenn eine Komponente einen Verweis auf eine `IDataProtector` über einen Aufruf von `CreateProtector`erhält, wird diese Referenz für mehrere Aufrufe von `Protect` und `Unprotect`verwendet.
>
>Bei einem `Unprotect`-Aufrufe wird CryptographicException ausgelöst, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann. Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs Cookies liest, kann diesen Fehler behandeln und die Anforderung so behandeln, als ob Sie überhaupt kein Cookie hätte, anstatt die Anforderung vollständig zu übernehmen. Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.
