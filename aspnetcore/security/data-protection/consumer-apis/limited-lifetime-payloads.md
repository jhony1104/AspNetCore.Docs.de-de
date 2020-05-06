---
title: Beschränken der Lebensdauer geschützter Nutzlasten in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die Lebensdauer einer geschützten Nutzlast mithilfe der ASP.net Core Datenschutz-APIs begrenzen.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768610"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="48d42-103">Beschränken der Lebensdauer geschützter Nutzlasten in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="48d42-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="48d42-104">Es gibt Szenarien, in denen der Anwendungsentwickler eine geschützte Nutzlast erstellen möchte, die nach einer festgelegten Zeitspanne abläuft.</span><span class="sxs-lookup"><span data-stu-id="48d42-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="48d42-105">Beispielsweise kann die geschützte Nutzlast ein Token zum Zurücksetzen von Kenn Wörtern darstellen, das nur eine Stunde gültig sein sollte.</span><span class="sxs-lookup"><span data-stu-id="48d42-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="48d42-106">Es ist für den Entwickler sicherlich möglich, ein eigenes Nutz Last Format zu erstellen, das ein eingebettetes Ablaufdatum enthält, und fortgeschrittene Entwickler möchten dies möglicherweise trotzdem tun, aber für die Mehrzahl der Entwickler, die diese Abläufe verwalten, kann es mühsam sein.</span><span class="sxs-lookup"><span data-stu-id="48d42-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="48d42-107">Das Paket [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) enthält hilfsprogrammapis zum Erstellen von Nutzlasten, die nach einem festgelegten Zeitraum automatisch ablaufen, um dies für unsere Entwickler Zielgruppe zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="48d42-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="48d42-108">Diese APIs hängen vom `ITimeLimitedDataProtector` Typ ab.</span><span class="sxs-lookup"><span data-stu-id="48d42-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="48d42-109">API-Verwendung</span><span class="sxs-lookup"><span data-stu-id="48d42-109">API usage</span></span>

<span data-ttu-id="48d42-110">Die `ITimeLimitedDataProtector` -Schnittstelle ist die Kernschnittstelle zum Schutz und zum Schutz von zeitlich begrenzten und selbst abgelaufenen Nutzlasten.</span><span class="sxs-lookup"><span data-stu-id="48d42-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="48d42-111">Zum Erstellen einer Instanz von benötigen `ITimeLimitedDataProtector`Sie zunächst eine Instanz einer regulären [idataprotector](xref:security/data-protection/consumer-apis/overview) , die mit einem bestimmten Zweck erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="48d42-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="48d42-112">Sobald die `IDataProtector` Instanz verfügbar ist, können Sie `IDataProtector.ToTimeLimitedDataProtector` die Erweiterungsmethode abrufen, um eine Schutzvorrichtung mit den integrierten Ablauf Funktionen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="48d42-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="48d42-113">`ITimeLimitedDataProtector`macht die folgenden API-Oberflächen-und Erweiterungs Methoden verfügbar:</span><span class="sxs-lookup"><span data-stu-id="48d42-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="48d42-114">Erstellungs Schutzvorrichtung (Zeichenfolge): itimelimiteddataprotector: Diese API ähnelt der vorhandenen `IDataProtectionProvider.CreateProtector` in, die verwendet werden kann, um [Zweck Ketten](xref:security/data-protection/consumer-apis/purpose-strings) aus einer Schutzvorrichtung mit eingeschränkter Stamm Zeit zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="48d42-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="48d42-115">Protect (Byte [] Klartext, DateTimeOffset-Ablauf): Byte []</span><span class="sxs-lookup"><span data-stu-id="48d42-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="48d42-116">Protect (Byte [] Klartext, TimeSpan-Lebensdauer): Byte []</span><span class="sxs-lookup"><span data-stu-id="48d42-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="48d42-117">Protect (Byte [] Klartext): Byte []</span><span class="sxs-lookup"><span data-stu-id="48d42-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="48d42-118">Protect (String Plaintext, DateTimeOffset-Ablauf): Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="48d42-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="48d42-119">Protect (Zeichenfolge Plaintext, TimeSpan Lifetime): String</span><span class="sxs-lookup"><span data-stu-id="48d42-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="48d42-120">Protect (Zeichenfolge Klartext): Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="48d42-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="48d42-121">Zusätzlich zu den Kern `Protect` Methoden, die nur den nur-Text verwenden, gibt es neue über Ladungen, die das Angeben des Ablaufdatums der Nutzlast ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="48d42-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="48d42-122">Das Ablaufdatum kann als absolutes Datum (über einen `DateTimeOffset`) oder als relative Zeit (von der aktuellen Systemzeit über einen `TimeSpan`) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="48d42-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="48d42-123">Wenn eine Überladung, die keine Ablaufzeit annimmt, aufgerufen wird, wird davon ausgegangen, dass die Nutzlast nie abläuft.</span><span class="sxs-lookup"><span data-stu-id="48d42-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="48d42-124">Schutz aufheben (Byte [] ProtectedData, Ausgabe DateTimeOffset): Byte []</span><span class="sxs-lookup"><span data-stu-id="48d42-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="48d42-125">Schutz aufheben (Byte [] ProtectedData): Byte []</span><span class="sxs-lookup"><span data-stu-id="48d42-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="48d42-126">Schutz aufheben (Zeichenfolge ProtectedData, out DateTimeOffset-Ablauf): Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="48d42-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="48d42-127">Schutz aufheben (Zeichenfolge ProtectedData): Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="48d42-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="48d42-128">Die `Unprotect` -Methoden geben die ursprünglichen ungeschützten Daten zurück.</span><span class="sxs-lookup"><span data-stu-id="48d42-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="48d42-129">Wenn die Nutzlast noch nicht abgelaufen ist, wird der absolute Ablauf zusammen mit den ursprünglichen ungeschützten Daten als optionaler out-Parameter zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="48d42-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="48d42-130">Wenn die Nutzlast abgelaufen ist, lösen alle über Ladungen der Methode zum Aufheben des Schutzes CryptographicException aus.</span><span class="sxs-lookup"><span data-stu-id="48d42-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="48d42-131">Es wird nicht empfohlen, diese APIs zu verwenden, um Nutzlasten zu schützen, für die eine langfristige oder unbegrenzte Persistenz erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="48d42-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="48d42-132">"Kann ich mir leisten, dass die geschützten Nutzlasten nach einem Monat dauerhaft nicht wieder hergestellt werden können?"</span><span class="sxs-lookup"><span data-stu-id="48d42-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="48d42-133">kann als gute Faustregel dienen. Wenn die Antwort nein lautet, sollten Entwickler alternative APIs in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="48d42-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="48d42-134">Im folgenden Beispiel werden die [nicht-di-Codepfade](xref:security/data-protection/configuration/non-di-scenarios) zum Instanziieren des Datenschutzsystems verwendet.</span><span class="sxs-lookup"><span data-stu-id="48d42-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="48d42-135">Um dieses Beispiel auszuführen, stellen Sie sicher, dass Sie zunächst einen Verweis auf das Paket Microsoft. aspnetcore. dataprotection. Extensions hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="48d42-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
