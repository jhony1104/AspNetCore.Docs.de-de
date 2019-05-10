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
# <a name="next-steps"></a><span data-ttu-id="e49d9-103">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="e49d9-103">Next steps</span></span>

<span data-ttu-id="e49d9-104">In diesem Handbuch haben Sie eine DevOps-Pipeline für eine ASP.NET Core-Beispiel-app erstellt.</span><span class="sxs-lookup"><span data-stu-id="e49d9-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="e49d9-105">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="e49d9-105">Congratulations!</span></span> <span data-ttu-id="e49d9-106">Wir hoffen, Sie fanden lernen, wie Sie ASP.NET Core-Web-apps in Azure App Service veröffentlichen, und automatisieren die kontinuierliche Integration von Änderungen.</span><span class="sxs-lookup"><span data-stu-id="e49d9-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="e49d9-107">Nach der Bereitstellung auf einem Webserver, und klicken Sie mit der DevOps hat Azure eine Vielzahl von Platform-as-a-Service (PaaS)-Dienste, die für ASP.NET Core-Entwickler nützlich.</span><span class="sxs-lookup"><span data-stu-id="e49d9-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="e49d9-108">Dieser Abschnitt enthält eine kurze Übersicht über einige der am häufigsten verwendeten Dienste.</span><span class="sxs-lookup"><span data-stu-id="e49d9-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="e49d9-109">Speicher und Datenbanken</span><span class="sxs-lookup"><span data-stu-id="e49d9-109">Storage and databases</span></span>

<span data-ttu-id="e49d9-110">[Redis-Cache](/azure/redis-cache/) hohem Durchsatz und geringer Latenz datenzwischenspeicherung als Dienst verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="e49d9-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="e49d9-111">Es kann für das Zwischenspeichern der Seitenausgabe datenbankanforderungen zu reduzieren und Bereitstellen von ASP.NET Core-Sitzungszustand über mehrere Instanzen einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e49d9-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="e49d9-112">[Azure-Speicher](/azure/storage/) massiv skalierbarer cloudspeicher von Azure ist.</span><span class="sxs-lookup"><span data-stu-id="e49d9-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="e49d9-113">Entwickler können nutzen [Queue Storage](/azure/storage/queues/storage-queues-introduction) zuverlässiges Message Queuing, und [Table Storage](/azure/storage/tables/table-storage-overview) ist ein NoSQL-Schlüssel-Wert-Datenspeicher für die schnelle Entwicklung mithilfe von massive, teilweise strukturierten Datasets entwickelt.</span><span class="sxs-lookup"><span data-stu-id="e49d9-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="e49d9-114">[Azure SQL-Datenbank](/azure/sql-database/) bietet relationale Datenbankfunktionalität als mit der Microsoft SQL Server-Engine-Dienst vertraut.</span><span class="sxs-lookup"><span data-stu-id="e49d9-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="e49d9-115">[COSMOS DB](/azure/cosmos-db/) Global verteilter NoSQL-Datenbankdienst.</span><span class="sxs-lookup"><span data-stu-id="e49d9-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="e49d9-116">Mehrere APIs sind verfügbar, einschließlich SQL-API (früher als DocumentDB bezeichnet), Cassandra und MongoDB.</span><span class="sxs-lookup"><span data-stu-id="e49d9-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="e49d9-117">Identität</span><span class="sxs-lookup"><span data-stu-id="e49d9-117">Identity</span></span>

<span data-ttu-id="e49d9-118">[Azure Active Directory](/azure/active-directory/) und [Azure Active Directory B2C](/azure/active-directory-b2c/) werden beide Identitätsdienste.</span><span class="sxs-lookup"><span data-stu-id="e49d9-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="e49d9-119">Azure Active Directory ist für Enterprise-Szenarios konzipiert und ermöglicht der Azure AD B2B Zusammenarbeit (Business-to-Business), während Azure Active Directory B2C gewünschten Unternehmen-zu-Kunde-Szenarien, einschließlich der Anmeldung für soziale Netzwerke ist.</span><span class="sxs-lookup"><span data-stu-id="e49d9-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="e49d9-120">Mobil</span><span class="sxs-lookup"><span data-stu-id="e49d9-120">Mobile</span></span>

<span data-ttu-id="e49d9-121">[Notification Hubs](/azure/notification-hubs/) ist eine plattformübergreifende und skalierbare pushbenachrichtigungs-Engine, Millionen von Nachrichten schnell an apps auf verschiedenen Arten von Geräten zu senden.</span><span class="sxs-lookup"><span data-stu-id="e49d9-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="e49d9-122">Webinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="e49d9-122">Web infrastructure</span></span>

<span data-ttu-id="e49d9-123">[Azure Container Service](/azure/aks/) verwaltet Ihre gehostete Kubernetes-Umgebung, sodass schnelle und einfache Bereitstellung und Verwaltung von containeranwendungen containerorchestrierung.</span><span class="sxs-lookup"><span data-stu-id="e49d9-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="e49d9-124">[Azure Search](/azure/search/) wird verwendet, um eine Lösung für Unternehmenssuche über private, heterogene Inhalte erstellen.</span><span class="sxs-lookup"><span data-stu-id="e49d9-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="e49d9-125">[Service Fabric](/azure/service-fabric/) ist eine Plattform für verteilte Systeme, die ganz einfach packen, bereitstellen und verwalten skalierbarer und zuverlässiger Microservices und Containern.</span><span class="sxs-lookup"><span data-stu-id="e49d9-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
