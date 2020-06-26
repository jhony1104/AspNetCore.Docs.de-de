---
title: Ansichts basierte Autorisierung in ASP.net Core MVC
author: rick-anderson
description: In diesem Dokument wird veranschaulicht, wie der Autorisierungs Dienst in einer ASP.net Core Ansicht eingefügt und verwendet wird Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: ce2365dbdcffc8c44d0d9d7cc6c9d29d0fe1895c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408928"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="79f74-103">Ansichts basierte Autorisierung in ASP.net Core MVC</span><span class="sxs-lookup"><span data-stu-id="79f74-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="79f74-104">Ein Entwickler möchte eine Benutzeroberfläche häufig auf der Grundlage der aktuellen Benutzeridentität anzeigen, ausblenden oder anderweitig ändern.</span><span class="sxs-lookup"><span data-stu-id="79f74-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="79f74-105">Sie können über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in MVC-Ansichten auf den Autorisierungs Dienst zugreifen.</span><span class="sxs-lookup"><span data-stu-id="79f74-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="79f74-106">Um den Autorisierungs Dienst in eine Razor Ansicht einzufügen, verwenden Sie die- `@inject` Direktive:</span><span class="sxs-lookup"><span data-stu-id="79f74-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="79f74-107">Wenn Sie den Autorisierungs Dienst in jeder Ansicht verwenden möchten, platzieren Sie die `@inject` Direktive in der Datei " *_ViewImports. cshtml* " des Verzeichnisses " *views* ".</span><span class="sxs-lookup"><span data-stu-id="79f74-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="79f74-108">Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="79f74-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="79f74-109">Verwenden Sie den injizierten Autorisierungs Dienst, um `AuthorizeAsync` genau die gleiche Weise aufzurufen, die Sie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)überprüfen würden:</span><span class="sxs-lookup"><span data-stu-id="79f74-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="79f74-110">In einigen Fällen ist die Ressource Ihr Ansichts Modell.</span><span class="sxs-lookup"><span data-stu-id="79f74-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="79f74-111">Rufen `AuthorizeAsync` Sie auf genau dieselbe Weise auf wie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="79f74-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="79f74-112">Im vorangehenden Code wird das Modell als Ressource übermittelt, die von der Richtlinien Auswertung berücksichtigt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="79f74-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="79f74-113">Verlassen Sie sich nicht auf das Umschalten der Sichtbarkeit der Benutzeroberflächen Elemente Ihrer APP als alleinige Autorisierungs Überprüfung.</span><span class="sxs-lookup"><span data-stu-id="79f74-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="79f74-114">Wenn Sie ein UI-Element ausblenden, kann der Zugriff auf die zugeordnete Controller Aktion nicht vollständig verhindert werden</span><span class="sxs-lookup"><span data-stu-id="79f74-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="79f74-115">Sehen Sie sich beispielsweise die Schaltfläche im vorangehenden Code Ausschnitt an.</span><span class="sxs-lookup"><span data-stu-id="79f74-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="79f74-116">Ein Benutzer kann die `Edit` Aktionsmethode aufrufen, wenn er weiß, dass die relative Ressourcen-URL */Document/Edit/1*lautet.</span><span class="sxs-lookup"><span data-stu-id="79f74-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="79f74-117">Aus diesem Grund sollte die `Edit` Aktionsmethode eine eigene Autorisierungs Überprüfung durchführen.</span><span class="sxs-lookup"><span data-stu-id="79f74-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
