---
title: Beschränken der Lebensdauer von geschützten Nutzlasten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Lebensdauer einer geschützten Nutzlast, die mit ASP.NET Core Datenschutz-APIs zu begrenzen.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897117"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Beschränken der Lebensdauer von geschützten Nutzlasten in ASP.NET Core

Es gibt Szenarien, in dem der Entwickler möchte eine geschützte Nutzlast zu erstellen, die nach einem festgelegten Zeitraum abläuft. Beispielsweise kann die geschützte Nutzlast ein kennwortzurücksetzungstoken darstellen, die nur eine Stunde lang gültig sein soll. Es ist sicherlich möglich, für den Entwickler eigene Nutzlast-Format zu erstellen, enthält ein eingebettetes Ablaufdatum, und erfahrene Entwickler und möchten möglicherweise trotzdem fortsetzen, aber für die meisten Entwickler verwalten diese Ablaufzeiten anwachsen kann mühsam.

Um dies für unsere Zielgruppe der Entwickler erstellt, das Paket vereinfachen [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) enthält Dienstprogramm-APIs zum Erstellen von Nutzlasten, die automatisch nach einem festgelegten Zeitraum ablaufen soll. Diese APIs der hängen die `ITimeLimitedDataProtector` Typ.

## <a name="api-usage"></a>API-Nutzung

Die `ITimeLimitedDataProtector` Schnittstelle ist die Kernschnittstelle zum Schützen und Aufheben des Schutzes zeitlich begrenzte / Self ablaufender-Nutzlasten. Zum Erstellen einer Instanz von einem `ITimeLimitedDataProtector`, benötigen Sie zunächst eine Instanz einer regulären [IDataProtector](xref:security/data-protection/consumer-apis/overview) erstellt, die mit einem bestimmten Zweck. Nach der `IDataProtector` Instanz verfügbar ist, rufen die `IDataProtector.ToTimeLimitedDataProtector` Erweiterungsmethode, um eine Schutzvorrichtung mit integrierten Ablauf Funktionen erhalten.

`ITimeLimitedDataProtector` Stellt die folgenden API-Oberfläche und die Erweiterung der Methoden an:

* CreateProtector (Zweck Zeichenfolge): ITimeLimitedDataProtector: Diese API ist mit dem vorhandenen `IDataProtectionProvider.CreateProtector` , da sie verwendet werden kann, erstellen [Ketten zu Entwicklungszwecken](xref:security/data-protection/consumer-apis/purpose-strings) aus einer Stamm-Schutzvorrichtung zeitlich begrenzte.

* Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]

* Protect(byte[] plaintext, TimeSpan lifetime) : byte[]

* Protect(byte[] plaintext) : byte[]

* Protect(string plaintext, DateTimeOffset expiration) : string

* Protect(string plaintext, TimeSpan lifetime) : string

* Schützen (Zeichenfolge nur-Text): Zeichenfolge

Zusätzlich zu den `Protect` Methoden, die dauern nur nur-Text, es gibt neue Überladungen, sodass der Nutzlast Ablaufdatum angeben. Das Ablaufdatum kann als ein absolutes Datum angegeben werden (über eine `DateTimeOffset`) oder als ein relativer Zeitpunkt (aus dem aktuellen System Zeit, über eine `TimeSpan`). Wenn eine Überladung, die ein Ablaufdatum akzeptieren, nicht aufgerufen wird, wird die Nutzlast angenommen, dass nie ablaufen.

* Aufheben des Schutzes (Byte [] ProtectedData, "DateTimeOffset"-Ablauf): Byte]

* Unprotect(byte[] protectedData) : byte[]

* Aufheben des Schutzes (Zeichenfolge ProtectedData, "DateTimeOffset"-Ablauf): Zeichenfolge

* Aufheben des Schutzes (ProtectedData Zeichenfolge): Zeichenfolge

Die `Unprotect` Methoden geben die ursprünglichen ungeschützten Daten zurück. Wenn die Nutzlast noch nicht abgelaufen ist, wird die absolute Ablaufzeit als eine optionale Ausgabeparameter zusammen mit den ursprünglichen ungeschützten Daten zurückgegeben. Wenn die Nutzlast abgelaufen ist, werden alle Überladungen der Methode Unprotect CryptographicException auslösen.

>[!WARNING]
> Es ist nicht auf diese APIs verwenden, um Nutzlasten zu schützen, die langfristige oder einer unbestimmten Persistenz erfordern, empfohlen. "Kann ich für die geschützten-Nutzlasten, um nach einem Monat dauerhaft nicht wiederhergestellt werden leisten?" dienen als gute Faustregel; Wenn die Antwort ist, sollten keine Entwickler dann alternative APIs.

Das Beispiel unten verwendet die [nicht-DI-Codepfade](xref:security/data-protection/configuration/non-di-scenarios) für das System zum Schutz von Daten zu instanziieren. Um dieses Beispiel ausführen zu können, stellen Sie sicher, dass Sie zuerst einen Verweis auf das Paket Microsoft.AspNetCore.DataProtection.Extensions hinzugefügt haben.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
