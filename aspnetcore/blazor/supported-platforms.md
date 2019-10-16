---
title: ASP.net Core von blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 4e86bd6967a747a59c99a515c1c838cc2c21770f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391221"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.net Core von blazor unterstützten Plattformen

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Browseranforderungen

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Aktuell               |
| Mozilla Firefox                  | Aktuell               |
| Google Chrome, einschließlich Android | Aktuell               |
| Safari, einschließlich IOS            | Aktuell               |
| Microsoft Internet Explorer      | Nicht unterstützt @ no__t-0 |

&dagger;Microsoft Internet Explorer unterstützt [Webassembly](https://webassembly.org)nicht.

### <a name="blazor-server"></a>Blazor Server

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Aktuell    |
| Mozilla Firefox                  | Aktuell    |
| Google Chrome, einschließlich Android | Aktuell    |
| Safari, einschließlich IOS            | Aktuell    |
| Microsoft Internet Explorer      | 11 @ no__t-0 |

&dagger;zusätzliche polyfills sind erforderlich (beispielsweise können Zusagen über ein [Polyfill.IO](https://polyfill.io/v3/) -Bundle hinzugefügt werden).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/hosting-models>
