---
title: 'Nächste Schritte: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Zusätzliche Lernpfade für DevOps mit ASP.NET Core und Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892007"
---
# <a name="next-steps"></a>Nächste Schritte

In diesem Handbuch haben Sie eine DevOps-Pipeline für eine ASP.NET Core-Beispiel-app erstellt. Herzlichen Glückwunsch! Wir hoffen, Sie fanden lernen, wie Sie ASP.NET Core-Web-apps in Azure App Service veröffentlichen, und automatisieren die kontinuierliche Integration von Änderungen.

Nach der Bereitstellung auf einem Webserver, und klicken Sie mit der DevOps hat Azure eine Vielzahl von Platform-as-a-Service (PaaS)-Dienste, die für ASP.NET Core-Entwickler nützlich. Dieser Abschnitt enthält eine kurze Übersicht über einige der am häufigsten verwendeten Dienste.

## <a name="storage-and-databases"></a>Speicher und Datenbanken

[Redis-Cache](/azure/redis-cache/) hohem Durchsatz und geringer Latenz datenzwischenspeicherung als Dienst verfügbar ist. Es kann für das Zwischenspeichern der Seitenausgabe datenbankanforderungen zu reduzieren und Bereitstellen von ASP.NET Core-Sitzungszustand über mehrere Instanzen einer App verwendet werden.

[Azure-Speicher](/azure/storage/) massiv skalierbarer cloudspeicher von Azure ist. Entwickler können nutzen [Queue Storage](/azure/storage/queues/storage-queues-introduction) zuverlässiges Message Queuing, und [Table Storage](/azure/storage/tables/table-storage-overview) ist ein NoSQL-Schlüssel-Wert-Datenspeicher für die schnelle Entwicklung mithilfe von massive, teilweise strukturierten Datasets entwickelt.

[Azure SQL-Datenbank](/azure/sql-database/) bietet relationale Datenbankfunktionalität als mit der Microsoft SQL Server-Engine-Dienst vertraut.

[COSMOS DB](/azure/cosmos-db/) Global verteilter NoSQL-Datenbankdienst. Mehrere APIs sind verfügbar, einschließlich SQL-API (früher als DocumentDB bezeichnet), Cassandra und MongoDB.

## <a name="identity"></a>Identität

[Azure Active Directory](/azure/active-directory/) und [Azure Active Directory B2C](/azure/active-directory-b2c/) werden beide Identitätsdienste. Azure Active Directory ist für Enterprise-Szenarios konzipiert und ermöglicht der Azure AD B2B Zusammenarbeit (Business-to-Business), während Azure Active Directory B2C gewünschten Unternehmen-zu-Kunde-Szenarien, einschließlich der Anmeldung für soziale Netzwerke ist.

## <a name="mobile"></a>Mobil

[Notification Hubs](/azure/notification-hubs/) ist eine plattformübergreifende und skalierbare pushbenachrichtigungs-Engine, Millionen von Nachrichten schnell an apps auf verschiedenen Arten von Geräten zu senden.

## <a name="web-infrastructure"></a>Webinfrastruktur

[Azure Container Service](/azure/aks/) verwaltet Ihre gehostete Kubernetes-Umgebung, sodass schnelle und einfache Bereitstellung und Verwaltung von containeranwendungen containerorchestrierung.

[Azure Search](/azure/search/) wird verwendet, um eine Lösung für Unternehmenssuche über private, heterogene Inhalte erstellen.

[Service Fabric](/azure/service-fabric/) ist eine Plattform für verteilte Systeme, die ganz einfach packen, bereitstellen und verwalten skalierbarer und zuverlässiger Microservices und Containern.
