---
title: ASP.net Core von blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/supported-platforms
ms.openlocfilehash: b769ee175cde7c9a613d7fb70949de129ca428d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211570"
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
