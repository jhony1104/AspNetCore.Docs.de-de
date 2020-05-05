---
title: Einführung in die Autorisierung in ASP.net Core
author: rick-anderson
description: Erlernen Sie die Grundlagen der Autorisierung und die Funktionsweise der Autorisierung in ASP.net Core apps.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: 241ef8b00e9dcbd1983d32edcd9c1db2eaa5c687
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777526"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="4dd9b-103">Einführung in die Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4dd9b-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="4dd9b-104">Die Autorisierung bezieht sich auf den Prozess, mit dem bestimmt wird, was ein Benutzer tun kann.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="4dd9b-105">Ein Administrator kann z. b. eine Dokumentbibliothek erstellen, Dokumente hinzufügen, Dokumente bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="4dd9b-106">Ein nicht administrativer Benutzer, der mit der Bibliothek arbeitet, ist nur zum Lesen der Dokumente autorisiert.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="4dd9b-107">Die Autorisierung ist orthogonal und unabhängig von der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="4dd9b-108">Die Autorisierung erfordert jedoch einen Authentifizierungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="4dd9b-109">Die Authentifizierung ist der Prozess, bei dem ermittelt wird, wer ein Benutzer ist.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="4dd9b-110">Die Authentifizierung kann mindestens eine Identität für den aktuellen Benutzer erstellen.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="4dd9b-111">Weitere Informationen zur Authentifizierung in ASP.net Core finden <xref:security/authentication/index>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="4dd9b-112">Autorisierungs Typen</span><span class="sxs-lookup"><span data-stu-id="4dd9b-112">Authorization types</span></span>

<span data-ttu-id="4dd9b-113">ASP.net Core Autorisierung bietet eine einfache, deklarative und umfassende [Richtlinien basierte](xref:security/authorization/policies) Modell [Funktion](xref:security/authorization/roles) .</span><span class="sxs-lookup"><span data-stu-id="4dd9b-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="4dd9b-114">Die Autorisierung wird in Anforderungen ausgedrückt, und Handler Werten die Ansprüche eines Benutzers anhand der Anforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="4dd9b-115">Imperative Überprüfungen können auf einfachen Richtlinien oder Richtlinien basieren, die sowohl die Benutzeridentität als auch die Eigenschaften der Ressource auswerten, auf die der Benutzer zugreifen möchte.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="4dd9b-116">Namespaces</span><span class="sxs-lookup"><span data-stu-id="4dd9b-116">Namespaces</span></span>

<span data-ttu-id="4dd9b-117">Autorisierungs Komponenten, einschließlich `AuthorizeAttribute` des `AllowAnonymousAttribute` -Attributs und des- `Microsoft.AspNetCore.Authorization` Attributs, finden Sie im-Namespace.</span><span class="sxs-lookup"><span data-stu-id="4dd9b-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="4dd9b-118">Informieren Sie sich in der Dokumentation über die [einfache Autorisierung](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="4dd9b-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
