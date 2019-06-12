---
title: Übersicht über Consumer-APIs für ASP.NET Core
author: rick-anderson
description: Erhalten Sie eine kurze Übersicht über die verschiedenen Consumer-APIs, die innerhalb der ASP.NET Core-Data-Protection-Bibliothek zur Verfügung.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837381"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Übersicht über Consumer-APIs für ASP.NET Core

Die `IDataProtectionProvider` und `IDataProtector` sind die grundlegenden Schnittstellen, die durch den Consumer System zum Schutz von Daten verwenden. Sie sind im Verzeichnis der [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) Paket.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

Die Provider-Schnittstelle stellt den Stamm des Data Protection Systems dar. Es kann nicht direkt zu schützen oder Aufheben des Schutzes von Daten verwendet werden. Stattdessen muss der Consumer Abrufen eines Verweises auf ein `IDataProtector` durch Aufrufen von `IDataProtectionProvider.CreateProtector(purpose)`, wobei der Zweck einer Zeichenfolge ist, die die gewünschten Consumer Anwendungsfall beschrieben wird. Finden Sie unter [Zweckzeichenfolgen](xref:security/data-protection/consumer-apis/purpose-strings) viel mehr Informationen auf der Absicht dieses Parameters und einen entsprechenden Wert auswählen.

## <a name="idataprotector"></a>IDataProtector

Die Schutzvorrichtung-Schnittstelle wird durch einen Aufruf zurückgegeben `CreateProtector`, und diese Schnittstelle, die Kunden verwenden können, führen Sie zu schützen und Aufheben des Schutzes Vorgänge.

Um Daten zu schützen, übergeben Sie die Daten in die `Protect` Methode. Die grundlegende Schnittstelle definiert eine Methode, die konvertiert Byte [] -> Byte [], aber es gibt auch eine Überladung (als eine Erweiterungsmethode angegeben) die Zeichenfolge konvertiert-Zeichenfolge >. Die Sicherheit mithilfe der beiden Methoden ist identisch. Entwickler sollten auswählen, welche Überladung für ihren Anwendungsfall am einfachsten ist. Unabhängig von der Überladung ausgewählt, den Rückgabewert von schützen Methode jetzt geschützt wird (dazu und vor Manipulationen vorabauflistung), und die Anwendung kann es zu einem nicht vertrauenswürdigen Client senden.

Übergeben Sie zum Aufheben des Schutzes von einer zuvor geschützten Dateneinheit, die geschützten Daten auf den `Unprotect` Methode. (Es sind Byte []-Basis und zeichenfolgenbasierten Überladungen der Einfachheit halber die Entwickler.) Wenn die geschützte Nutzlast durch einen früheren Aufruf von generiert wurde `Protect` auf diesem gleichen `IDataProtector`, wird die `Unprotect` Methode gibt die ursprüngliche nicht geschützte Nutzlast zurück. Wenn die geschützte Nutzlast manipuliert wurde oder von einem anderen erzeugt wurde `IDataProtector`, `Unprotect` Methode löst CryptographicException.

Das Konzept des gleichen im Vergleich zu anderen `IDataProtector` Ties zu sichern, um das Konzept der Zweck. Wenn zwei `IDataProtector` Instanzen aus demselben Stamm generiert wurden `IDataProtectionProvider` jedoch über unterschiedliche Purpose-Zeichenfolgen im Aufruf von `IDataProtectionProvider.CreateProtector`, und klicken Sie dann diese berücksichtigt werden [verschiedene Schutzvorrichtungen](xref:security/data-protection/consumer-apis/purpose-strings), und eine nicht zum Aufheben des Schutzes möglich Nutzlasten, die von den anderen generiert werden.

## <a name="consuming-these-interfaces"></a>Nutzen diese Schnittstellen

Eine Komponente zur Abhängigkeitsinjektion die beabsichtigte Verwendung ist, dass die Komponente akzeptiert eine `IDataProtectionProvider` Parameter in seinem Konstruktor, und das DI-System diesen Dienst automatisch bereitgestellt, wenn die Komponente instanziiert wird.

> [!NOTE]
> Einige Anwendungen (z. B. konsolenanwendungen oder ASP.NET 4.x-Anwendungen) möglicherweise nicht zur Abhängigkeitsinjektion, damit hier beschriebenen Mechanismus nicht verwenden können. Diese Szenarien finden Sie in der [nicht DI Szenarien](xref:security/data-protection/configuration/non-di-scenarios) Dokument für Weitere Informationen zum Abrufen einer Instanz von einer `IDataProtection` Anbieter ohne Umweg über Dependency Injection.

Das folgende Beispiel zeigt drei Konzepte:

1. [Fügen Sie das System zum Schutz von Daten](xref:security/data-protection/configuration/overview) an den Container Service

2. Mithilfe der Abhängigkeitsinjektion zum Empfangen von einer Instanz von einem `IDataProtectionProvider`, und

3. Erstellen einer `IDataProtector` aus einem `IDataProtectionProvider` und zum Schützen und Aufheben des Schutzes von Daten.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Das Paket Microsoft.AspNetCore.DataProtection.Abstractions enthält eine Erweiterungsmethode `IServiceProvider.GetDataProtector` Developer zur Vereinfachung. Kapselt einen einzelnen Vorgang sowohl Abrufen einer `IDataProtectionProvider` aus der Dienstanbieter und der Aufruf `IDataProtectionProvider.CreateProtector`. Das folgende Beispiel veranschaulicht die Verwendung.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` für mehrere Aufrufer threadsicher sind. Es ist vorgesehen, die nach eine Komponente einen Verweis auf Ruft eine `IDataProtector` über einen Aufruf an `CreateProtector`, verwendet diesen Verweis für mehrere Aufrufe `Protect` und `Unprotect`. Ein Aufruf von `Unprotect` CryptographicException wird ausgelöst, wenn die geschützte Nutzlast überprüft oder entschlüsselt werden kann. Einige Komponenten ggf. Fehler zu ignorieren Aufheben des Schutzes von während der Vorgänge. eine Komponente, die Authentifizierungs-Cookies liest möglicherweise diesen Fehler zu behandeln und die Anforderung zu behandeln, als ob er überhaupt kein Cookie hätte statt, tritt ein Anforderungsfehler 30-tägiges. Komponenten, die dieses Verhalten soll, sollten speziell CryptographicException abfangen, statt alle Ausnahmen schlucken.
