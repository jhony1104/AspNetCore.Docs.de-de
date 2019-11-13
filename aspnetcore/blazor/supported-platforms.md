---
title: ASP.net Core Blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/supported-platforms
ms.openlocfilehash: de51296cc8785474e1c1406cfd5d4e5bd4050172
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962731"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.net Core Blazor unterstützten Plattformen

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Browseranforderungen

### <a name="opno-locblazor-webassembly"></a>Webassembly Blazor

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Aktuell               |
| Mozilla Firefox                  | Aktuell               |
| Google Chrome, einschließlich Android | Aktuell               |
| Safari, einschließlich IOS            | Aktuell               |
| Microsoft Internet Explorer      | Nicht unterstützt&dagger; |

&dagger;Microsoft Internet Explorer unterstützt [Webassembly](https://webassembly.org)nicht.

### <a name="opno-locblazor-server"></a>Blazor Server

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Aktuell    |
| Mozilla Firefox                  | Aktuell    |
| Google Chrome, einschließlich Android | Aktuell    |
| Safari, einschließlich IOS            | Aktuell    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;zusätzliche polyfills sind erforderlich (beispielsweise können Zusagen über ein [Polyfill.IO](https://polyfill.io/v3/) -Bundle hinzugefügt werden).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/hosting-models>
