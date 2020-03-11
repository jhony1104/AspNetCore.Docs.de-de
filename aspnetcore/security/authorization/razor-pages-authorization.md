---
title: Razor Pages Autorisierungs Konventionen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653113"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor Pages Autorisierungs Konventionen in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden. Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten. In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie). Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen. Um ASP.net Core Identität zu verwenden, befolgen Sie die Anweisungen in <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Autorisierung für den Zugriff auf eine Seite erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Seite eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann mit dem `[Authorize]` Filter-Attribut auf eine Seiten Modell Klasse angewendet werden. Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Bereichs Seite unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Bereichen in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Anonymen Zugriff auf einen Ordner von Seiten zulassen

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff

Es ist zulässig, anzugeben, dass für einen Ordner von Seiten eine Autorisierung erforderlich ist, und dann anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Das Gegenteil ist jedoch nicht gültig. Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl. Wenn die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat der <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden. Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten. In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie). Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen. Um ASP.net Core Identität zu verwenden, befolgen Sie die Anweisungen in <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Autorisierung für den Zugriff auf eine Seite erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Seite eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann mit dem `[Authorize]` Filter-Attribut auf eine Seiten Modell Klasse angewendet werden. Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.

Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Bereichs Seite unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Bereichen in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich. Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.

Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad hinzuzufügen:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Anonymen Zugriff auf einen Ordner von Seiten zulassen

Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hinzuzufügen:

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

Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl. Wenn die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat der <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
