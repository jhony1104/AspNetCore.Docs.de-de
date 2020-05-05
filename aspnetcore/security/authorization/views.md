---
title: Ansichts basierte Autorisierung in ASP.net Core MVC
author: rick-anderson
description: In diesem Dokument wird veranschaulicht, wie der Autorisierungs Dienst in einer ASP.net Core Razor Ansicht eingefügt und verwendet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775621"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Ansichts basierte Autorisierung in ASP.net Core MVC

Ein Entwickler möchte eine Benutzeroberfläche häufig auf der Grundlage der aktuellen Benutzeridentität anzeigen, ausblenden oder anderweitig ändern. Sie können über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in MVC-Ansichten auf den Autorisierungs Dienst zugreifen. Um den Autorisierungs Dienst in eine Razor Ansicht einzufügen, verwenden `@inject` Sie die-Direktive:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Wenn Sie den Autorisierungs Dienst in jeder Ansicht verwenden möchten, `@inject` platzieren Sie die Direktive in der Datei " *_ViewImports. cshtml* " des Verzeichnisses " *views* ". Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).

Verwenden Sie den injizierten Autorisierungs `AuthorizeAsync` Dienst, um genau die gleiche Weise aufzurufen, die Sie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)überprüfen würden:

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

In einigen Fällen ist die Ressource Ihr Ansichts Modell. Rufen `AuthorizeAsync` Sie auf genau dieselbe Weise auf wie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

Im vorangehenden Code wird das Modell als Ressource übermittelt, die von der Richtlinien Auswertung berücksichtigt werden sollte.

> [!WARNING]
> Verlassen Sie sich nicht auf das Umschalten der Sichtbarkeit der Benutzeroberflächen Elemente Ihrer APP als alleinige Autorisierungs Überprüfung. Wenn Sie ein UI-Element ausblenden, kann der Zugriff auf die zugeordnete Controller Aktion nicht vollständig verhindert werden Sehen Sie sich beispielsweise die Schaltfläche im vorangehenden Code Ausschnitt an. Ein Benutzer kann die `Edit` Aktionsmethode aufrufen, wenn er weiß, dass die relative Ressourcen-URL */Document/Edit/1*lautet. Aus diesem Grund sollte die `Edit` Aktionsmethode eine eigene Autorisierungs Überprüfung durchführen.
