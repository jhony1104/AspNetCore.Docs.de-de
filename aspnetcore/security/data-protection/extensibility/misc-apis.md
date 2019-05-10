---
title: Verschiedene ASP.NET Core, die Datenschutz-APIs
author: rick-anderson
description: Erfahren Sie mehr über die ASP.NET Core Data Protection ISecret-Schnittstelle.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896617"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="a1263-103">Verschiedene ASP.NET Core, die Datenschutz-APIs</span><span class="sxs-lookup"><span data-stu-id="a1263-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="a1263-104">Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.</span><span class="sxs-lookup"><span data-stu-id="a1263-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="a1263-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="a1263-105">ISecret</span></span>

<span data-ttu-id="a1263-106">Die `ISecret` Schnittstelle darstellt, einen geheimen Wert ein, z.B. kryptografische Schlüsseldaten.</span><span class="sxs-lookup"><span data-stu-id="a1263-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="a1263-107">Es enthält die folgende API-Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="a1263-107">It contains the following API surface:</span></span>

* <span data-ttu-id="a1263-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="a1263-108">`Length`: `int`</span></span>

* <span data-ttu-id="a1263-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="a1263-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="a1263-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="a1263-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="a1263-111">Die `WriteSecretIntoBuffer` Methode füllt den angegebenen Puffer mit den unformatierten geheimen Wert.</span><span class="sxs-lookup"><span data-stu-id="a1263-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="a1263-112">Der Grund, diese API den Puffer als Parameter wird, und gibt keinen `byte[]` direkt ist, dass dadurch dem Aufrufer die Möglichkeit, die das Pufferobjekt, das Geheimnis der verwaltete Garbage Collector eingeschränkt anheften.</span><span class="sxs-lookup"><span data-stu-id="a1263-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="a1263-113">Die `Secret` Typ ist eine konkrete Implementierung der `ISecret` , in denen der Wert des geheimen Schlüssels im prozessinterne Arbeitsspeicher gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a1263-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="a1263-114">Auf Windows-Plattformen wird der Wert des geheimen Schlüssels über verschlüsselt [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="a1263-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
