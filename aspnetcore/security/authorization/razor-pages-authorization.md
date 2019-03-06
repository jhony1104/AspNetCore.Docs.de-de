---
title: Autorisierungskonventionen für Razor Pages in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Zugriff auf Seiten mit den Konventionen steuern, die Autorisierung von Benutzern und Fall jeder anonyme Benutzer den Zugriff auf Seiten oder Ordner von Seiten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 040d33eba7eaf7a3aece2eedcdef7343e52972af
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345500"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Autorisierungskonventionen für Razor Pages in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Eine Möglichkeit zum Steuern des Zugriffs in Ihrer app für Razor Pages ist autorisierungskonventionen beim Start verwendet. Diese Konventionen können Sie zum Autorisieren von Benutzern und Fall jeder anonyme Benutzer den Zugriff auf einzelne Seiten oder Ordner von Seiten. Das Übernehmen von Konventionen, die automatisch in diesem Thema beschriebenen [Autorisierungsfilter](xref:mvc/controllers/filters#authorization-filters) zum Steuern des Zugriffs.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die beispielanwendung verwendet [Cookieauthentifizierung ohne ASP.NET Core Identity](xref:security/authentication/cookie). Die Konzepte und die Beispiele in diesem Thema gelten auch für apps, die ASP.NET Core Identity verwenden. Um ASP.NET Core Identity zu verwenden, befolgen Sie die Anleitung im <xref:security/authentication/identity>.

## <a name="require-authorization-to-access-a-page"></a>Eine Autorisierung auf eine Seite zuzugreifen

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zur Seite mit den angegebenen Pfad:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad, der Razor-Seiten Stamm relative Pfad ohne eine Erweiterung und enthält, die nur Schrägstriche handelt.

Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizePage Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann angewendet werden, um eine Seite-Model-Klasse mit dem `[Authorize]` Standardsicherheitsfilter-Attribut. Weitere Informationen finden Sie unter [Authorize-Filter-Attribut](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Eine Autorisierung auf einen Ordner der Seiten zuzugreifen

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> für alle Seiten in einem Ordner unter dem angegebenen Pfad:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad, der relative Pfad des Razor-Seiten-Stamm handelt.

Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeFolder Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Autorisierung zum Zugriff auf die Bereichsseite ein erforderlich

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf der Seite "Bereich", unter dem angegebenen Pfad:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Der Seitenname ist der Pfad der Datei ohne Erweiterung relativ zum Stammverzeichnis Seiten für den angegebenen Bereich. Z. B. der Seitenname für die Datei *Areas/Identity/Pages/Manage/Accounts.cshtml* ist */Manage/Konten*.

Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeAreaPage Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Eine Autorisierung auf einen Ordner mit Bereichen

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf alle Bereiche in einem Ordner unter dem angegebenen Pfad:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Der Ordnerpfad ist der Pfad des Ordners, relativ zum Stammverzeichnis Seiten für den angegebenen Bereich. Beispielsweise ist der Ordnerpfad für die Dateien unter *Bereiche/Identity/Pages/verwalten/* ist */Verwalten von*.

Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeAreaFolder Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Anonymen Zugriff auf eine Seite zulassen

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad, der Razor-Seiten Stamm relative Pfad ohne eine Erweiterung und enthält, die nur Schrägstriche handelt.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Zulassen des anonymen Zugriffs in einem Ordner von Seiten

Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> für alle Seiten in einem Ordner unter dem angegebenen Pfad:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Der angegebene Pfad ist der Ansichts-Engine-Pfad, der relative Pfad des Razor-Seiten-Stamm handelt.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Hinweis zur Kombination von autorisiert und anonymer Zugriff

Es ist zulässig, die angeben, dass ein Ordner der Seiten, die Autorisierung erforderlich ist und als anzugeben, dass eine Seite in diesem Ordner den anonymen Zugriff zulässt:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Es ist allerdings das Gegenteil ist ungültig. Sie können nicht deklariert einen Ordner der Seiten, die für den anonymen Zugriff, und geben Sie dann auf eine Seite in diesem Ordner, die Autorisierung erforderlich ist:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Erfordert Autorisierung auf der Seite "Privat" schlägt fehl. Wenn sowohl die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> gelten auf der Seite die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hat Vorrang vor und steuert den Zugriff.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
