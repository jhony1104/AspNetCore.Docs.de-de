---
title: Übersicht über Consumer-APIs für ASP.net Core
author: rick-anderson
description: Erhalten Sie einen kurzen Überblick über die verschiedenen Consumer-APIs, die in der ASP.net Core Datenschutz Bibliothek verfügbar sind.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654583"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Übersicht über Consumer-APIs für ASP.net Core

Die `IDataProtectionProvider`-und `IDataProtector` Schnittstellen sind die grundlegenden Schnittstellen, über die Consumer das Datenschutzsystem verwenden. Sie befinden sich im Paket [Microsoft. aspnetcore. dataprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Die Anbieter Schnittstelle stellt den Stamm des Datenschutzsystems dar. Sie kann nicht direkt verwendet werden, um Daten zu schützen oder deren Schutz aufzuheben. Der Consumer muss stattdessen einen Verweis auf einen `IDataProtector` abrufen, indem er `IDataProtectionProvider.CreateProtector(purpose)`aufgerufen, wobei Zweck eine Zeichenfolge ist, die den vorgesehenen consumerfallfall beschreibt. Weitere Informationen zum Zweck dieses Parameters und zum Auswählen eines geeigneten Werts finden Sie unter [Zweck](xref:security/data-protection/consumer-apis/purpose-strings) Zeichenfolgen.

## <a name="idataprotector"></a>IDataProtector

Die Schutzvorrichtung wird von einem-`CreateProtector`zurückgegeben, und es handelt sich um diese Schnittstelle, mit der Consumer Vorgänge zum Schutz und zum Aufheben des Schutzes durchführen können.

Um ein Datenelement zu schützen, übergeben Sie die Daten an die `Protect`-Methode. Die grundlegende Schnittstelle definiert eine Methode, die Byte []-> Byte [] konvertiert, aber es gibt auch eine Überladung (als Erweiterungsmethode bereitgestellt), die Zeichen folgen > Zeichenfolge konvertiert. Die von den beiden Methoden angebotene Sicherheit ist identisch. der Entwickler sollte auswählen, welche Überladung für den Anwendungsfall am bequemsten ist. Unabhängig von der gewählten Überladung wird der von der Protect-Methode zurückgegebene Wert nun geschützt (verschlüsselt und manipuliert), und die Anwendung kann Sie an einen nicht vertrauenswürdigen Client senden.

Um den Schutz für ein zuvor geschütztes Datenelement aufzuheben, übergeben Sie die geschützten Daten an die `Unprotect`-Methode. (Es gibt Byte []-basierte und Zeichen folgen basierte über Ladungen für Entwickler.) Wenn die geschützte Nutzlast durch einen früheren `Protect` auf demselben `IDataProtector`generiert wurde, gibt die `Unprotect`-Methode die ursprüngliche ungeschützte Nutzlast zurück. Wenn die geschützte Nutzlast manipuliert wurde oder von einer anderen `IDataProtector`erstellt wurde, löst die `Unprotect`-Methode CryptographicException aus.

Das Konzept desselben und verschiedener `IDataProtector` verknüpft mit dem Konzept des zwecks. Wenn zwei `IDataProtector` Instanzen aus demselben Stamm `IDataProtectionProvider` generiert wurden, aber über unterschiedliche Zweck Zeichenfolgen im Aufruf von `IDataProtectionProvider.CreateProtector`, dann werden Sie als [unterschiedliche Schutz](xref:security/data-protection/consumer-apis/purpose-strings)Vorrichtungen angesehen, und eine kann nicht den Schutz von Nutzlasten aufheben, die vom anderen generiert werden.

## <a name="consuming-these-interfaces"></a>Verwenden dieser Schnittstellen

Bei einer di-fähigen Komponente besteht die beabsichtigte Verwendung darin, dass die Komponente einen `IDataProtectionProvider`-Parameter im Konstruktor annimmt und dass das di-System diesen Dienst automatisch bereitstellt, wenn die Komponente instanziiert wird.

> [!NOTE]
> Einige Anwendungen (z. b. Konsolen Anwendungen oder ASP.NET 4. x-Anwendungen) sind möglicherweise nicht di-fähig und können den hier beschriebenen Mechanismus nicht verwenden. Weitere Informationen zum Abrufen einer Instanz eines `IDataProtection` Anbieters ohne die Verwendung von di in diesen Szenarien finden Sie im Dokument [Szenarios mit nicht-di](xref:security/data-protection/configuration/non-di-scenarios) -Unterstützung.

Im folgenden Beispiel werden drei Konzepte veranschaulicht:

1. [Hinzufügen des Datenschutzsystems](xref:security/data-protection/configuration/overview) zum Dienst Container

2. Verwenden von di zum Empfangen einer Instanz einer `IDataProtectionProvider`und

3. Erstellen einer `IDataProtector` aus einer `IDataProtectionProvider` und deren Verwendung zum schützen und Aufheben des Schutzes von Daten.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Das Paket Microsoft. aspnetcore. dataprotection. Abstractions enthält eine Erweiterungsmethode, die `IServiceProvider.GetDataProtector` als Entwickler freundlicher ist. Sie wird als einzelner Vorgang gekapselt, wobei sowohl eine `IDataProtectionProvider` vom Dienstanbieter abgerufen als auch `IDataProtectionProvider.CreateProtector`aufgerufen wird. Im folgenden Beispiel wird die Verwendung veranschaulicht.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher. Wenn eine Komponente einen Verweis auf eine `IDataProtector` über einen Aufruf von `CreateProtector`erhält, wird diese Referenz für mehrere Aufrufe von `Protect` und `Unprotect`verwendet. Bei einem `Unprotect`-Aufrufe wird CryptographicException ausgelöst, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann. Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs Cookies liest, kann diesen Fehler behandeln und die Anforderung so behandeln, als ob Sie überhaupt kein Cookie hätte, anstatt die Anforderung vollständig zu übernehmen. Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.
