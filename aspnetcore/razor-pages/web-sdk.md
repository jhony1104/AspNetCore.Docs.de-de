---
title: ASP.net Core-Web-SDK
author: Rick-Anderson
description: Übersicht über Microsoft. net. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 49e21e1a432149409a01550452cedf4009dcfba7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830645"
---
# <a name="aspnet-core-web-sdk"></a>ASP.net Core-Web-SDK

### <a name="overview"></a>Übersicht über

`Microsoft.NET.Sdk.Web` ist ein [MSBuild-Projekt-SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) zum Erstellen von ASP.net Core-apps. Es ist möglich, eine ASP.net Core-App ohne dieses SDK zu erstellen, aber das Web-SDK ist:

* Sie sind auf die Bereitstellung eines erstklassigen Erlebnisses zugeschnitten.
* Das empfohlene Ziel für die meisten Benutzer.

Verwenden Sie das Web. SDK in einem Projekt:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Mit dem Web SDK aktivierte Funktionen:

* Projekte, die auf .net Core 3,0 oder höher abzielen, verweisen implizit:

  * Der [ASP.net Core freigegebenen Frameworks](xref:fundamentals/metapackage-app).
  * [Analyse](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) Tools, die für die ASP.net Core-apps entwickelt wurden.
* Das WebSDK aktiviert MSBuild-Ziele, die die Verwendung von Veröffentlichungs Profilen und die Veröffentlichung mit Webbereitstellung ermöglichen.

### <a name="properties"></a>Eigenschaften

| Die Eigenschaften- | Beschreibung |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Deaktiviert den impliziten Verweis auf den `Microsoft.AspNetCore.App` freigegebenen Frameworks. |
| `DisableImplicitAspNetCoreAnalyzers` | Deaktiviert den impliziten Verweis auf ASP.net Core-Analyzers. |
| `DisableImplicitComponentsAnalyzers` | Deaktiviert den impliziten Verweis auf Razor-Komponenten Analysen beim Entwickeln von Blazor (Server)-Anwendungen. |
