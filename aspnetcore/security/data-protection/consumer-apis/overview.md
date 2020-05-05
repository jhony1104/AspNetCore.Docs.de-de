---
title: Übersicht über Consumer-APIs für ASP.net Core
author: rick-anderson
description: Erhalten Sie einen kurzen Überblick über die verschiedenen Consumer-APIs, die in der ASP.net Core Datenschutz Bibliothek verfügbar sind.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767856"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Übersicht über Consumer-APIs für ASP.net Core

Die `IDataProtectionProvider` - `IDataProtector` Schnittstelle und die-Schnittstelle sind die Basis Schnittstellen, über die Consumer das Datenschutzsystem verwenden. Sie befinden sich im Paket [Microsoft. aspnetcore. dataprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>Idataschutzprovider

Die Anbieter Schnittstelle stellt den Stamm des Datenschutzsystems dar. Sie kann nicht direkt verwendet werden, um Daten zu schützen oder deren Schutz aufzuheben. Stattdessen muss der Consumer einen Verweis auf einen `IDataProtector` abrufen, indem er `IDataProtectionProvider.CreateProtector(purpose)`aufgerufen wird, wobei Zweck eine Zeichenfolge ist, die den vorgesehenen consumerfallfall beschreibt. Weitere Informationen zum Zweck dieses Parameters und zum Auswählen eines geeigneten Werts finden Sie unter [Zweck](xref:security/data-protection/consumer-apis/purpose-strings) Zeichenfolgen.

## <a name="idataprotector"></a>Idataprotector

Die Schutzvorrichtung wird durch einen-Rückruf zurück `CreateProtector`gegeben, und es handelt sich um diese Schnittstelle, mit der Consumer Vorgänge zum Schutz und zum Aufheben des Schutzes durchführen können.

Um ein Datenelement zu schützen, übergeben Sie die Daten an `Protect` die-Methode. Die grundlegende Schnittstelle definiert eine Methode, die Byte []-> Byte [] konvertiert, aber es gibt auch eine Überladung (als Erweiterungsmethode bereitgestellt), die Zeichen folgen > Zeichenfolge konvertiert. Die von den beiden Methoden angebotene Sicherheit ist identisch. der Entwickler sollte auswählen, welche Überladung für den Anwendungsfall am bequemsten ist. Unabhängig von der gewählten Überladung wird der von der Protect-Methode zurückgegebene Wert nun geschützt (verschlüsselt und manipuliert), und die Anwendung kann Sie an einen nicht vertrauenswürdigen Client senden.

Um den Schutz für ein zuvor geschütztes Datenelement aufzuheben, übergeben Sie die geschützten Daten an `Unprotect` die-Methode. (Es gibt Byte []-basierte und Zeichen folgen basierte über Ladungen für Entwickler.) Wenn die geschützte Nutzlast von einem früheren-Befehl in `Protect` generiert wurde `IDataProtector`, gibt die `Unprotect` -Methode die ursprüngliche ungeschützte Nutzlast zurück. Wenn die geschützte Nutzlast manipuliert wurde oder von einer anderen `IDataProtector`erstellt wurde, löst die `Unprotect` Methode CryptographicException aus.

Das Konzept der gleichen und unterschiedlichen `IDataProtector` Verknüpfungen zurück zum Konzept des zwecks. Wenn zwei `IDataProtector` Instanzen aus demselben Stamm generiert wurden, `IDataProtectionProvider` aber über unterschiedliche Zweck Zeichenfolgen im `IDataProtectionProvider.CreateProtector`Aufruf von, werden Sie als [unterschiedliche Schutz](xref:security/data-protection/consumer-apis/purpose-strings)Vorrichtungen angesehen, und eine kann nicht den Schutz von Nutzlasten aufheben, die von der anderen generiert werden.

## <a name="consuming-these-interfaces"></a>Verwenden dieser Schnittstellen

Bei einer di-fähigen Komponente besteht die beabsichtigte Verwendung darin, dass die Komponente einen `IDataProtectionProvider` -Parameter im Konstruktor annimmt und dass das di-System diesen Dienst automatisch bereitstellt, wenn die Komponente instanziiert wird.

> [!NOTE]
> Einige Anwendungen (z. b. Konsolen Anwendungen oder ASP.NET 4. x-Anwendungen) sind möglicherweise nicht di-fähig und können den hier beschriebenen Mechanismus nicht verwenden. Informationen zu diesen Szenarien finden Sie in den Dokumenten zu nicht-di-fähigen [Szenarios](xref:security/data-protection/configuration/non-di-scenarios) , um `IDataProtection` Weitere Informationen zum Abrufen einer Instanz eines Anbieters ohne Verwendung von di zu erhalten.

Im folgenden Beispiel werden drei Konzepte veranschaulicht:

1. [Hinzufügen des Datenschutzsystems](xref:security/data-protection/configuration/overview) zum Dienst Container

2. Verwenden von di zum Empfangen einer Instanz von `IDataProtectionProvider`und

3. Erstellen eines `IDataProtector` aus einem `IDataProtectionProvider` und verwenden dieses zum schützen und Aufheben des Schutzes von Daten.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Das Paket Microsoft. aspnetcore. dataprotection. Abstractions enthält eine Erweiterungsmethode `IServiceProvider.GetDataProtector` , die für den Entwickler von Vorteil ist. Sie wird als einzelner Vorgang gekapselt, wobei sowohl `IDataProtectionProvider` ein vom Dienstanbieter abgerufen als `IDataProtectionProvider.CreateProtector`auch aufgerufen wird. Im folgenden Beispiel wird die Verwendung veranschaulicht.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher. Wenn eine Komponente einen Verweis auf `IDataProtector` einen über einen Aufruf von `CreateProtector`erhält, wird dieser Verweis für mehrere Aufrufe von `Protect` und `Unprotect`verwendet. Ein-Aufrufe `Unprotect` von löst CryptographicException aus, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann. Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs Cookies liest, kann diesen Fehler behandeln und die Anforderung so behandeln, als ob Sie überhaupt kein Cookie hätte, anstatt die Anforderung vollständig zu übernehmen. Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.
