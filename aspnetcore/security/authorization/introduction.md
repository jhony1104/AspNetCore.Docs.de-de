---
title: Einführung in die Autorisierung in ASP.NET Core
author: rick-anderson
description: Enthält die Grundlagen der Autorisierung und die Funktionsweise der Autorisierung in ASP.NET Core-apps.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896977"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="c268e-103">Einführung in die Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c268e-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="c268e-104">Autorisierung bezieht sich auf den Prozess, der bestimmt, was ein Benutzer möglich ist.</span><span class="sxs-lookup"><span data-stu-id="c268e-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="c268e-105">Beispielsweise kann ein Administrator eine Dokumentbibliothek erstellen, Dokumente hinzufügen, Bearbeiten von Dokumenten und löschen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="c268e-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="c268e-106">Ein Benutzer ohne Administratorrechte arbeiten mit der Bibliothek wird nur autorisierte, um die Dokumente zu lesen.</span><span class="sxs-lookup"><span data-stu-id="c268e-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="c268e-107">Autorisierung ist orthogonal und unabhängig von der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c268e-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="c268e-108">Allerdings erfordert eine Autorisierung einen Authentifizierungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="c268e-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="c268e-109">Authentifizierung ist der Prozess der Feststellung, wer ein Benutzer ist.</span><span class="sxs-lookup"><span data-stu-id="c268e-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="c268e-110">Authentifizierung kann eine oder mehrere Identitäten für den aktuellen Benutzer erstellen.</span><span class="sxs-lookup"><span data-stu-id="c268e-110">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="c268e-111">Autorisierungstypen</span><span class="sxs-lookup"><span data-stu-id="c268e-111">Authorization types</span></span>

<span data-ttu-id="c268e-112">ASP.NET Core-Autorisierung stellt eine einfache, deklarative [Rolle](xref:security/authorization/roles) und einen umfangreichen [richtlinienbasierten](xref:security/authorization/policies) Modell.</span><span class="sxs-lookup"><span data-stu-id="c268e-112">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="c268e-113">Autorisierung Anforderungen ausgedrückt wird, und Handler auswerten, die Benutzeransprüche im Hinblick auf Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c268e-113">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="c268e-114">Imperative Überprüfungen können auf einfache Richtlinien oder Richtlinien ausgewertet werden, sowohl die Identität des Benutzers als auch die Eigenschaften der Ressource, die der Benutzer zugreifen möchte, basieren.</span><span class="sxs-lookup"><span data-stu-id="c268e-114">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="c268e-115">Namespaces</span><span class="sxs-lookup"><span data-stu-id="c268e-115">Namespaces</span></span>

<span data-ttu-id="c268e-116">Authorization-Komponenten, einschließlich der `AuthorizeAttribute` und `AllowAnonymousAttribute` Attribute befinden sich die `Microsoft.AspNetCore.Authorization` Namespace.</span><span class="sxs-lookup"><span data-stu-id="c268e-116">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="c268e-117">In der Dokumentation auf [einfache Autorisierung](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="c268e-117">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
