---
title: ASP.net Core von blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 8730417f772c84ebcccc449a5826126aa5c64abb
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168169"
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
| Microsoft Internet Explorer      | Nicht unterstützt&dagger; |

&dagger;[Webassembly](https://webassembly.org)wird von Microsoft Internet Explorer nicht unterstützt.

### <a name="blazor-server"></a>Blazor Server

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Aktuell    |
| Mozilla Firefox                  | Aktuell    |
| Google Chrome, einschließlich Android | Aktuell    |
| Safari, einschließlich IOS            | Aktuell    |
| Microsoft Internet Explorer      | 11:&dagger; |

&dagger;Zusätzliche polyfills sind erforderlich (Zusagen können z. b. über ein [Polyfill.IO](https://polyfill.io/v3/) -Bundle hinzugefügt werden).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/hosting-models>
