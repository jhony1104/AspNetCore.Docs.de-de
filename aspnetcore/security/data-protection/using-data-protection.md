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
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="cbcd1-103">Erste Schritte mit Datenschutz-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cbcd1-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="cbcd1-104">Die einfachsten und den Schutz von Daten besteht die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="cbcd1-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="cbcd1-105">Erstellen Sie eine Schutzvorrichtung, aus einen Datenschutzanbieter.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="cbcd1-106">Rufen Sie die `Protect` Methode mit den Daten, die Sie schützen möchten.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="cbcd1-107">Rufen Sie die `Unprotect` Methode mit den Daten, die Sie in nur-Text wieder aktivieren möchten.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="cbcd1-108">Die meisten Frameworks und app-Modelle, z.B. ASP.NET Core oder SignalR, bereits System zum Schutz von Daten konfigurieren, und fügen sie einen Dienstcontainer, auf die, den Sie über Dependency Injection zugreifen, hinzu.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="cbcd1-109">Das folgende Beispiel zeigt einen Dienstcontainer für Dependency Injection konfigurieren und Registrieren der Stapel für den Schutz von Daten, Empfangen der Datenschutzanbieter über Dependency Injection, erstellen eine Schutzvorrichtung und schützen und Aufheben des Schutzes von Daten.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="cbcd1-110">Wenn Sie eine Schutzvorrichtung erstellen müssen Sie angeben, eine oder mehrere [Zweckzeichenfolgen](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="cbcd1-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="cbcd1-111">Eine Zeichenfolge Zweck bietet Isolierung zwischen Consumer.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="cbcd1-112">Beispielsweise wäre eine Schutzvorrichtung, die mit einer Zeichenfolge der Zweck der "Green" erstellt Aufheben des Schutzes von Daten, die durch eine Schutzvorrichtung bereitgestellt, mit dem Zweck "Violett" können nicht.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="cbcd1-113">Instanzen von `IDataProtectionProvider` und `IDataProtector` für mehrere Aufrufer threadsicher sind.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="cbcd1-114">Es ist vorgesehen, die nach eine Komponente einen Verweis auf Ruft eine `IDataProtector` über einen Aufruf an `CreateProtector`, verwendet diesen Verweis für mehrere Aufrufe `Protect` und `Unprotect`.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="cbcd1-115">Ein Aufruf von `Unprotect` CryptographicException wird ausgelöst, wenn die geschützte Nutzlast überprüft oder entschlüsselt werden kann.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="cbcd1-116">Einige Komponenten ggf. Fehler zu ignorieren Aufheben des Schutzes von während der Vorgänge. eine Komponente, die Authentifizierungs-Cookies liest möglicherweise diesen Fehler zu behandeln und die Anforderung zu behandeln, als ob er überhaupt kein Cookie hätte statt, tritt ein Anforderungsfehler 30-tägiges.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="cbcd1-117">Komponenten, die dieses Verhalten soll, sollten speziell CryptographicException abfangen, statt alle Ausnahmen schlucken.</span><span class="sxs-lookup"><span data-stu-id="cbcd1-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
