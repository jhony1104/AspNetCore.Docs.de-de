---
title: Ansichtsbasierte Autorisierung in ASP.NET Core MVC
author: rick-anderson
description: In diesem Dokument wird das Einfügen und Verwenden des autorisierungsdiensts in einer ASP.NET Core Razor-Ansicht veranschaulicht.
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892057"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Ansichtsbasierte Autorisierung in ASP.NET Core MVC

Ein Entwickler möchte häufig einblenden, ausblenden oder anderweitig ändern eine Benutzeroberfläche, die basierend auf der Identität des aktuellen Benutzers. Sie erreichen des autorisierungsdiensts in MVC-Ansichten über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection). Um den Autorisierungsdienst in einer Razor-Ansicht einfügen möchten, verwenden Sie die `@inject` Richtlinie:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Platzieren Sie Sie ggf. den Autorisierungsdienst in jeder Ansicht der `@inject` -Direktive in der *_ViewImports.cshtml* Datei die *Ansichten* Verzeichnis. Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).

Verwenden Sie den eingefügten Autorisierungsdienst Aufrufen `AuthorizeAsync` auf genau die gleiche Weise Sie, während der überprüfen würden [ressourcenbasierte Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

In einigen Fällen wird die Ressource Ihrem Ansichtsmodell sein. Rufen Sie `AuthorizeAsync` auf genau die gleiche Weise Sie, während der überprüfen würden [ressourcenbasierte Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

Im vorangehenden Code wird das Modell als Ressource, die die richtlinienauswertung durchführen soll übergeben, zu berücksichtigen.

> [!WARNING]
> Verlassen Sie sich nicht auf die Umschaltfläche Sichtbarkeit von Ihrer app-UI-Elementen wie die einzige autorisierungsprüfung. Ausblenden von einem Element der Benutzeroberfläche möglicherweise nicht vollständig Zugriff auf der zugehörigen Controlleraktion verhindern. Betrachten Sie beispielsweise die Schaltfläche im vorhergehenden Codeausschnitt aus. Benutzer kann Aufrufen der `Edit` Action-Methode, wenn er die relative Ressource kennt-URL ist */Document/Edit/1*. Aus diesem Grund die `Edit` Aktionsmethode sollten eigene autorisierungsprüfung ausführen.
