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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Sonstige ASP.net Core Datenschutz-APIs

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.

## <a name="isecret"></a>Isecret

Die `ISecret`-Schnittstelle stellt einen geheimen Wert dar, z. b. Material für kryptografische Schlüssel. Es enthält die folgende API-Oberfläche:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

Die `WriteSecretIntoBuffer`-Methode füllt den angegebenen Puffer mit dem unformatierten geheimen Wert auf. Der Grund, warum diese API den Puffer als Parameter annimmt, anstatt eine `byte[]` direkt zurückzugeben, besteht darin, dass der Aufrufer die Möglichkeit erhält, das Puffer Objekt anzuheften, wodurch die geheime Gefährdung des verwalteten Garbage Collector beschränkt wird.

Der `Secret`-Typ ist eine konkrete Implementierung von `ISecret`, bei der der geheime Wert im Prozess internen Speicher gespeichert wird. Auf Windows-Plattformen wird der geheime Wert über " [cryptprotectmemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)" verschlüsselt.
