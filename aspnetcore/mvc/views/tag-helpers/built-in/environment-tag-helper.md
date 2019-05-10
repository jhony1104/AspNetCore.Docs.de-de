---
title: Umgebungstaghilfsprogramm in ASP.NET Core
author: pkellner
description: Definition des Umgebungstaghilfsprogramms für ASP.NET Core, einschließlich aller Eigenschaften
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 379f58ed37329f047d53adf1dcfdfd2ad6a6ca4e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887675"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Umgebungstaghilfsprogramm in ASP.NET Core

Von [Peter Kellner](http://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya) und [Luke Latham](https://github.com/guardrex)

Basierend auf der aktuellen [Hostingumgebung](xref:fundamentals/environments) rendert das Environment-Taghilfsprogramm den von ihm eingeschlossenen Inhalt unter Vorbehalt. Das einzelne Attribut `names` des Environment-Taghilfsprogramm ist eine durch Trennzeichen getrennte Liste von Umgebungsnamen. Wenn die Namen der bereitgestellten Umgebung der aktuellen Umgebung entsprechen, wird der eingeschlossene Inhalt gerendert.

Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Attribute von Umgebungstaghilfsprogrammen

### <a name="names"></a>Namen

`names` akzeptiert den Namen einer einzelnen Hostingumgebung oder eine durch Trennzeichen getrennte Liste mit Namen von Hostingumgebungen, die das Rendering des eingeschlossenen Inhalts auslösen.

Umgebungswerte werden im Vergleich zum aktuellen Wert von [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) zurückgegeben. Bei dem Vergleich wird die Groß-/Kleinschreibung ignoriert.

Im folgenden Beispiel wird ein Environment-Taghilfsprogramm verwendet: Der Inhalt wird wiedergegeben, wenn es sich bei der Hostumgebung um eine Staging- oder Produktionsumgebung handelt:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Die Attribute „include“ und „exclude“

Die Attribute `include` & `exclude` steuern das Rendern des eingeschlossenen Inhalts anhand der eingeschlossenen bzw. ausgeschlossenen Namen von Hostingumgebungen.

### <a name="include"></a>include

Die `include`-Eigenschaft zeigt ein ähnliches Verhalten wie das Attribut `names`. Eine im Attributwert `include` aufgeführte Umgebung muss der App-Hostingumgebung ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) entsprechen, damit der Inhalt des Tags `<environment>` gerendert werden kann.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

Im Gegensatz zum Attribut `include` wird der Inhalt des `<environment>`-Tags gerendert, wenn die Hostingumgebung nicht einer Umgebung entspricht, die im Attributwert `exclude` aufgeführt wird.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/environments>
