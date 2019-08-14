---
title: Razor Pages Autorisierungs Konventionen in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994035"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor Pages Autorisierungs Konventionen in ASP.net Core

Von [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden. Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten. In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie). Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen. Um ASP.net Core Identität zu verwenden, befolgen Sie die <xref:security/authentication/identity>Anweisungen unter.

## <a name="require-authorization-to-access-a-page"></a>Autorisierung für den Zugriff auf eine Seite erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Seite auf dem angegebenen Pfad ein hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem `[Authorize]` Filter-Attribut angewendet werden. Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> einem alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> eine einer Seite im angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Anonymen Zugriff auf einen Ordner von Seiten zulassen

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff

Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Das Gegenteil ist jedoch nicht gültig. Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl. Wenn sowohl <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> als auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden. Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten. In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie). Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen. Um ASP.net Core Identität zu verwenden, befolgen Sie die <xref:security/authentication/identity>Anweisungen unter.

## <a name="require-authorization-to-access-a-page"></a>Autorisierung für den Zugriff auf eine Seite erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Seite auf dem angegebenen Pfad ein hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem `[Authorize]` Filter-Attribut angewendet werden. Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> einem alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> eine einer Seite im angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Anonymen Zugriff auf einen Ordner von Seiten zulassen

Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff

Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Das Gegenteil ist jedoch nicht gültig. Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl. Wenn sowohl <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> als auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
