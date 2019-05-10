---
title: Erste Schritte mit Datenschutz-APIs in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit der ASP.NET Core die Datenschutz-APIs für den Schutz und Schutz von Daten in einer app aufheben.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087637"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Erste Schritte mit Datenschutz-APIs in ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Die einfachsten und den Schutz von Daten besteht die folgenden Schritte aus:

1. Erstellen Sie eine Schutzvorrichtung, aus einen Datenschutzanbieter.

2. Rufen Sie die `Protect` Methode mit den Daten, die Sie schützen möchten.

3. Rufen Sie die `Unprotect` Methode mit den Daten, die Sie in nur-Text wieder aktivieren möchten.

Die meisten Frameworks und app-Modelle, z.B. ASP.NET Core oder SignalR, bereits System zum Schutz von Daten konfigurieren, und fügen sie einen Dienstcontainer, auf die, den Sie über Dependency Injection zugreifen, hinzu. Das folgende Beispiel zeigt einen Dienstcontainer für Dependency Injection konfigurieren und Registrieren der Stapel für den Schutz von Daten, Empfangen der Datenschutzanbieter über Dependency Injection, erstellen eine Schutzvorrichtung und schützen und Aufheben des Schutzes von Daten.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Wenn Sie eine Schutzvorrichtung erstellen müssen Sie angeben, eine oder mehrere [Zweckzeichenfolgen](xref:security/data-protection/consumer-apis/purpose-strings). Eine Zeichenfolge Zweck bietet Isolierung zwischen Consumer. Beispielsweise wäre eine Schutzvorrichtung, die mit einer Zeichenfolge der Zweck der "Green" erstellt Aufheben des Schutzes von Daten, die durch eine Schutzvorrichtung bereitgestellt, mit dem Zweck "Violett" können nicht.

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` für mehrere Aufrufer threadsicher sind. Es ist vorgesehen, die nach eine Komponente einen Verweis auf Ruft eine `IDataProtector` über einen Aufruf an `CreateProtector`, verwendet diesen Verweis für mehrere Aufrufe `Protect` und `Unprotect`.
>
>Ein Aufruf von `Unprotect` CryptographicException wird ausgelöst, wenn die geschützte Nutzlast überprüft oder entschlüsselt werden kann. Einige Komponenten ggf. Fehler zu ignorieren Aufheben des Schutzes von während der Vorgänge. eine Komponente, die Authentifizierungs-Cookies liest möglicherweise diesen Fehler zu behandeln und die Anforderung zu behandeln, als ob er überhaupt kein Cookie hätte statt, tritt ein Anforderungsfehler 30-tägiges. Komponenten, die dieses Verhalten soll, sollten speziell CryptographicException abfangen, statt alle Ausnahmen schlucken.
