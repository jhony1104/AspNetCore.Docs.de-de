---
title: ASP.net Core von blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 042fbb1b2c7f92b7dc6443319f3f195a12a55adc
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963881"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.net Core von blazor unterstützten Plattformen

Von [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Browseranforderungen

### <a name="blazor-webassembly"></a>Blazor-Webassembly

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Aktuell               |
| Mozilla Firefox                  | Aktuell               |
| Google Chrome, einschließlich Android | Aktuell               |
| Safari, einschließlich IOS            | Aktuell               |
| Microsoft Internet Explorer      | Nicht unterstützt&dagger; |

&dagger;[Webassembly](https://webassembly.org)wird von Microsoft Internet Explorer nicht unterstützt.

### <a name="blazor-server"></a>Blazor-Server

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
