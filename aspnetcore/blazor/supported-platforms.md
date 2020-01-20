---
title: ASP.net Core Blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160131"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.net Core Blazor unterstützten Plattformen

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Browseranforderungen

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft-Edge                   | Aktuell               |
| Mozilla Firefox                  | Aktuell               |
| Google Chrome, einschließlich Android | Aktuell               |
| Safari, einschließlich IOS            | Aktuell               |
| Microsoft Internet Explorer      | Nicht unterstützt&dagger; |

&dagger;Microsoft Internet Explorer unterstützt [Webassembly](https://webassembly.org)nicht.

### <a name="opno-locblazor-server"></a>Blazor Server

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft-Edge                   | Aktuell    |
| Mozilla Firefox                  | Aktuell    |
| Google Chrome, einschließlich Android | Aktuell    |
| Safari, einschließlich IOS            | Aktuell    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;zusätzlichen polyfills sind erforderlich (z. b. Zusagen können über ein [Polyfill.IO](https://polyfill.io/v3/) -Bundle hinzugefügt werden).

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:blazor/hosting-models>
