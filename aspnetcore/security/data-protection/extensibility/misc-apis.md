---
title: Sonstige ASP.net Core Datenschutz-APIs
author: rick-anderson
description: Erfahren Sie mehr über die isecret-Schnittstelle für ASP.net Core Datenschutz.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654355"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="a4f83-103">Sonstige ASP.net Core Datenschutz-APIs</span><span class="sxs-lookup"><span data-stu-id="a4f83-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="a4f83-104">Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.</span><span class="sxs-lookup"><span data-stu-id="a4f83-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="a4f83-105">Isecret</span><span class="sxs-lookup"><span data-stu-id="a4f83-105">ISecret</span></span>

<span data-ttu-id="a4f83-106">Die `ISecret`-Schnittstelle stellt einen geheimen Wert dar, z. b. Material für kryptografische Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="a4f83-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="a4f83-107">Es enthält die folgende API-Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="a4f83-107">It contains the following API surface:</span></span>

* <span data-ttu-id="a4f83-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="a4f83-108">`Length`: `int`</span></span>

* <span data-ttu-id="a4f83-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="a4f83-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="a4f83-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="a4f83-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="a4f83-111">Die `WriteSecretIntoBuffer`-Methode füllt den angegebenen Puffer mit dem unformatierten geheimen Wert auf.</span><span class="sxs-lookup"><span data-stu-id="a4f83-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="a4f83-112">Der Grund, warum diese API den Puffer als Parameter annimmt, anstatt eine `byte[]` direkt zurückzugeben, besteht darin, dass der Aufrufer die Möglichkeit erhält, das Puffer Objekt anzuheften, wodurch die geheime Gefährdung des verwalteten Garbage Collector beschränkt wird.</span><span class="sxs-lookup"><span data-stu-id="a4f83-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="a4f83-113">Der `Secret`-Typ ist eine konkrete Implementierung von `ISecret`, bei der der geheime Wert im Prozess internen Speicher gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="a4f83-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="a4f83-114">Auf Windows-Plattformen wird der geheime Wert über " [cryptprotectmemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)" verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="a4f83-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
