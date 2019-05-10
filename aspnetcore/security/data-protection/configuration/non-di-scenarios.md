---
title: Nicht-DI-Szenarien für den Schutz von Daten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Data Protection-Szenarien unterstützt werden, in denen Sie nicht oder keinen Dienst von Abhängigkeitsinjektion verwenden möchten.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 34354c8443f6ae00bcce6ad9bdb6c11aaaa25bf8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897307"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Nicht-DI-Szenarien für den Schutz von Daten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das ASP.NET Core-Datenschutz-System handelt es sich üblicherweise [hinzugefügt, um einen Dienstcontainer](xref:security/data-protection/consumer-apis/overview) von abhängigen Komponenten über Dependency Injection (DI) genutzt. Es gibt jedoch Fälle, in denen dies nicht möglich ist oder gewünscht ist, insbesondere, wenn das System in eine vorhandene app zu importieren.

Zur Unterstützung dieser Szenarios, die [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) Paket enthält einen konkreten Typ, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), welcher bietet eine einfache Möglichkeit zum Schutz von Daten verwenden ohne Abhängigkeit von DI. Die `DataProtectionProvider` -Typ implementiert [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Erstellen von `DataProtectionProvider` erfordert nur die Bereitstellung einer [DirectoryInfo](/dotnet/api/system.io.directoryinfo) Instanz, um anzugeben, in denen kryptografische Schlüssel des Anbieters gespeichert werden sollen, wie im folgenden Codebeispiel gezeigt:

[!code-none[](non-di-scenarios/_static/nodisample1.cs)]

In der Standardeinstellung die `DataProtectionProvider` konkreter Typ nicht unformatierten Schlüsselmaterial zu verschlüsseln vor dem Speichern im Dateisystem. Dies ist zur Unterstützung von Szenarien, in denen die Entwickler-verweist auf eine Netzwerkfreigabe und das System den Schutz von Daten automatisch einen entsprechenden ruhender Schlüsselverschlüsselung Mechanismus hergeleitet werden können.

Darüber hinaus die `DataProtectionProvider` konkreter Typ nicht [isolieren apps](xref:security/data-protection/configuration/overview#per-application-isolation) standardmäßig. Alle apps, die mit dem gleichen Schlüssel Verzeichnis können so lange wie Nutzlasten Freigeben ihrer [Parameter zu Entwicklungszwecken](xref:security/data-protection/consumer-apis/purpose-strings) übereinstimmen.

Die [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) -Konstruktor akzeptiert einen optionale Konfiguration-Rückruf, der verwendet werden kann, um das Verhalten des Systems anzupassen. Im folgenden Beispiel wird veranschaulicht, Wiederherstellen Isolierung durch einen expliziten Aufruf [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). Das Beispiel zeigt außerdem das Konfigurieren des Systems automatisch persistenter Schlüssel, die per Windows DPAPI verschlüsselt. Wenn das Verzeichnis in einer UNC-Freigabe verweist, werden Sie am besten ein gemeinsam genutztes Zertifikat auf allen relevanten Computern zu verteilen und zum Konfigurieren des Systems zum Verwenden der zertifikatbasierten Verschlüsselung mit einem Aufruf von [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-none[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Instanzen der `DataProtectionProvider` konkreter Typ sind aufwendig zu erstellen. Wenn eine app mehrere Instanzen dieses Typs verwaltet und sie alle das gleiche Verzeichnis für den Schlüsselspeicher verwenden, beeinträchtigt möglicherweise die Leistung der app. Bei Verwendung der `DataProtectionProvider` Typ, es wird empfohlen, dass Sie dieses Typs einmal erstellen und so weit wie möglich wiederverwendet. Die `DataProtectionProvider` Typ und alle [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) daraus erstellte Instanzen sind threadsicher für mehrere Aufrufer.
