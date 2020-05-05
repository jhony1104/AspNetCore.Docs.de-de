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
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="f5a7f-103">Ansichts basierte Autorisierung in ASP.net Core MVC</span><span class="sxs-lookup"><span data-stu-id="f5a7f-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="f5a7f-104">Ein Entwickler möchte eine Benutzeroberfläche häufig auf der Grundlage der aktuellen Benutzeridentität anzeigen, ausblenden oder anderweitig ändern.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="f5a7f-105">Sie können über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in MVC-Ansichten auf den Autorisierungs Dienst zugreifen.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f5a7f-106">Um den Autorisierungs Dienst in eine Razor Ansicht einzufügen, verwenden `@inject` Sie die-Direktive:</span><span class="sxs-lookup"><span data-stu-id="f5a7f-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="f5a7f-107">Wenn Sie den Autorisierungs Dienst in jeder Ansicht verwenden möchten, `@inject` platzieren Sie die Direktive in der Datei " *_ViewImports. cshtml* " des Verzeichnisses " *views* ".</span><span class="sxs-lookup"><span data-stu-id="f5a7f-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="f5a7f-108">Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f5a7f-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="f5a7f-109">Verwenden Sie den injizierten Autorisierungs `AuthorizeAsync` Dienst, um genau die gleiche Weise aufzurufen, die Sie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)überprüfen würden:</span><span class="sxs-lookup"><span data-stu-id="f5a7f-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="f5a7f-110">In einigen Fällen ist die Ressource Ihr Ansichts Modell.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="f5a7f-111">Rufen `AuthorizeAsync` Sie auf genau dieselbe Weise auf wie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="f5a7f-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="f5a7f-112">Im vorangehenden Code wird das Modell als Ressource übermittelt, die von der Richtlinien Auswertung berücksichtigt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="f5a7f-113">Verlassen Sie sich nicht auf das Umschalten der Sichtbarkeit der Benutzeroberflächen Elemente Ihrer APP als alleinige Autorisierungs Überprüfung.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="f5a7f-114">Wenn Sie ein UI-Element ausblenden, kann der Zugriff auf die zugeordnete Controller Aktion nicht vollständig verhindert werden</span><span class="sxs-lookup"><span data-stu-id="f5a7f-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="f5a7f-115">Sehen Sie sich beispielsweise die Schaltfläche im vorangehenden Code Ausschnitt an.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="f5a7f-116">Ein Benutzer kann die `Edit` Aktionsmethode aufrufen, wenn er weiß, dass die relative Ressourcen-URL */Document/Edit/1*lautet.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="f5a7f-117">Aus diesem Grund sollte die `Edit` Aktionsmethode eine eigene Autorisierungs Überprüfung durchführen.</span><span class="sxs-lookup"><span data-stu-id="f5a7f-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
