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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Verschiedene ASP.NET Core, die Datenschutz-APIs

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.

## <a name="isecret"></a>ISecret

Die `ISecret` Schnittstelle darstellt, einen geheimen Wert ein, z.B. kryptografische Schlüsseldaten. Es enthält die folgende API-Oberfläche:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

Die `WriteSecretIntoBuffer` Methode füllt den angegebenen Puffer mit den unformatierten geheimen Wert. Der Grund, diese API den Puffer als Parameter wird, und gibt keinen `byte[]` direkt ist, dass dadurch dem Aufrufer die Möglichkeit, die das Pufferobjekt, das Geheimnis der verwaltete Garbage Collector eingeschränkt anheften.

Die `Secret` Typ ist eine konkrete Implementierung der `ISecret` , in denen der Wert des geheimen Schlüssels im prozessinterne Arbeitsspeicher gespeichert. Auf Windows-Plattformen wird der Wert des geheimen Schlüssels über verschlüsselt [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
