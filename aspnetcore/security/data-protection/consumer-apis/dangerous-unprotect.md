---
title: Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in einer ASP.net Core-App aufheben können, die mit Schlüsseln geschützt sind, die seit dem widerrufen wurden
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 062703fc72ab4e515a99558b3316070ce1f83f79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776798"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="61a99-103">Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden</span><span class="sxs-lookup"><span data-stu-id="61a99-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="61a99-104">Die ASP.net Core-Datenschutz-APIs sind nicht in erster Linie für die unbegrenzte Persistenz von vertraulichen Nutzlasten vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="61a99-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="61a99-105">Andere Technologien wie [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) und [Azure Rights Management](/rights-management/) sind besser für das Szenario der unbegrenzten Speicherung geeignet und verfügen über die entsprechenden leistungsstarken Schlüssel Verwaltungsfunktionen.</span><span class="sxs-lookup"><span data-stu-id="61a99-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="61a99-106">Dies bedeutet, dass ein Entwickler nicht daran hindert, die ASP.net Core Datenschutz-APIs für den langfristigen Schutz vertraulicher Daten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="61a99-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="61a99-107">Schlüssel werden nie aus dem Schlüsselbund entfernt. so `IDataProtector.Unprotect` können vorhandene Nutzlasten immer wieder hergestellt werden, solange die Schlüssel verfügbar und gültig sind.</span><span class="sxs-lookup"><span data-stu-id="61a99-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="61a99-108">Es tritt jedoch ein Problem auf, wenn der Entwickler versucht, den Schutz von Daten aufzuheben, die mit einem gesperrten Schlüssel `IDataProtector.Unprotect` geschützt wurden, da in diesem Fall eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="61a99-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="61a99-109">Dies kann bei kurzlebigen oder vorübergehenden Nutzlasten (wie z. b. Authentifizierungs Token) in Ordnung sein, da diese Arten von Nutzlasten problemlos vom System neu erstellt werden können und sich der Standort Besucher im schlimmsten Fall erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="61a99-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="61a99-110">Für persistente Nutzlasten könnte das `Unprotect` Auslösen von "throw" zu nicht akzeptablen Datenverlusten führen.</span><span class="sxs-lookup"><span data-stu-id="61a99-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="61a99-111">Ipersisteddataprotector</span><span class="sxs-lookup"><span data-stu-id="61a99-111">IPersistedDataProtector</span></span>

<span data-ttu-id="61a99-112">Um das Szenario zu unterstützen, in dem Nutzlasten auch bei widerrufenen Schlüsseln nicht geschützt werden können, enthält das Daten `IPersistedDataProtector` Schutzsystem einen-Typ.</span><span class="sxs-lookup"><span data-stu-id="61a99-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="61a99-113">Um `IPersistedDataProtector`eine Instanz von zu erhalten, können Sie einfach eine `IDataProtector` Instanz von in der normalen Weise erhalten und `IDataProtector` versuchen `IPersistedDataProtector`, die in umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="61a99-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="61a99-114">Nicht alle `IDataProtector` Instanzen können in `IPersistedDataProtector`umgewandelt werden.</span><span class="sxs-lookup"><span data-stu-id="61a99-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="61a99-115">Entwickler sollten den c#-Operator oder ähnlich verwenden, um Lauf Zeit Ausnahmen zu vermeiden, die durch ungültige Umwandlungen verursacht werden, und Sie sollten darauf vorbereitet sein, den Fehlerfall entsprechend zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="61a99-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="61a99-116">`IPersistedDataProtector`macht die folgende API-Oberfläche verfügbar:</span><span class="sxs-lookup"><span data-stu-id="61a99-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="61a99-117">Diese API übernimmt die geschützte Nutzlast (als Bytearray) und gibt die ungeschützte Nutzlast zurück.</span><span class="sxs-lookup"><span data-stu-id="61a99-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="61a99-118">Es gibt keine Zeichen folgen basierte Überladung.</span><span class="sxs-lookup"><span data-stu-id="61a99-118">There's no string-based overload.</span></span> <span data-ttu-id="61a99-119">Die zwei out-Parameter lauten wie folgt.</span><span class="sxs-lookup"><span data-stu-id="61a99-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="61a99-120">`requiresMigration`: wird auf "true" festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, nicht mehr der aktive Standardschlüssel ist, z. b. wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, veraltet ist und ein Schlüssel rollender Vorgang stattfindet.</span><span class="sxs-lookup"><span data-stu-id="61a99-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="61a99-121">Der Aufrufer möchte ggf. den erneuten Schutz der Nutzlast abhängig von Ihren geschäftlichen Anforderungen in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="61a99-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="61a99-122">`wasRevoked`: wird auf true festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wurde, widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="61a99-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="61a99-123">Gehen Sie beim übergeben `ignoreRevocationErrors: true` an die `DangerousUnprotect` -Methode mit äußerster Vorsicht vor.</span><span class="sxs-lookup"><span data-stu-id="61a99-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="61a99-124">Wenn nach dem Aufrufen dieser Methode `wasRevoked` der Wert true ist, wurde der zum Schutz dieser Nutzlast verwendete Schlüssel widerrufen, und die Authentizität der Nutzlast sollte als Fehler verdächtig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="61a99-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="61a99-125">In diesem Fall wird nur der Betrieb der nicht geschützten Nutzlast fortgesetzt, wenn Sie eine separate Gewissheit haben, dass Sie authentisch ist, z. b., dass Sie von einer sicheren Datenbank stammt, anstatt von einem nicht vertrauenswürdigen WebClient gesendet zu werden.</span><span class="sxs-lookup"><span data-stu-id="61a99-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
