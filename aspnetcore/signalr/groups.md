---
title: Verwalten von Benutzern und Gruppen in SignalR
author: bradygaster
description: Übersicht über ASP.net Core SignalR Benutzer-und Gruppenverwaltung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 7e8c85dcbc46daa68988374f499f19a9d2cead47
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654247"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a><span data-ttu-id="521fc-103">Verwalten von Benutzern und Gruppen in SignalR</span><span class="sxs-lookup"><span data-stu-id="521fc-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="521fc-104">Von [Brennan](https://github.com/BrennanConroy) "a"</span><span class="sxs-lookup"><span data-stu-id="521fc-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="521fc-105"> ermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind, sowie an benannte Gruppen von Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="521fc-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="521fc-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="521fc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-opno-locsignalr"></a><span data-ttu-id="521fc-107">Benutzer in SignalR</span><span class="sxs-lookup"><span data-stu-id="521fc-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="521fc-108"> ermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="521fc-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="521fc-109">Standardmäßig verwendet SignalR die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal`, die der Verbindung zugeordnet ist, als Benutzer Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="521fc-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="521fc-110">Ein einzelner Benutzer kann über mehrere Verbindungen mit einer SignalR-App verfügen.</span><span class="sxs-lookup"><span data-stu-id="521fc-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="521fc-111">Beispielsweise könnte ein Benutzer sowohl auf seinem Desktop als auch auf seinem Telefon verbunden sein.</span><span class="sxs-lookup"><span data-stu-id="521fc-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="521fc-112">Jedes Gerät verfügt über eine separate SignalR Verbindung, die allerdings dem gleichen Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="521fc-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="521fc-113">Wenn eine Nachricht an den Benutzer gesendet wird, erhalten alle Verbindungen, die diesem Benutzer zugeordnet sind, die Nachricht.</span><span class="sxs-lookup"><span data-stu-id="521fc-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="521fc-114">Der Benutzer Bezeichner für eine Verbindung kann über die `Context.UserIdentifier`-Eigenschaft in ihrem Hub aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="521fc-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="521fc-115">Senden Sie eine Nachricht an einen bestimmten Benutzer, indem Sie die Benutzer-ID an die `User`-Funktion in der Hub-Methode übergeben, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="521fc-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="521fc-116">Bei der Benutzerkennung wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="521fc-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a><span data-ttu-id="521fc-117">Gruppen in SignalR</span><span class="sxs-lookup"><span data-stu-id="521fc-117">Groups in SignalR</span></span>

<span data-ttu-id="521fc-118">Eine Gruppe ist eine Auflistung von Verbindungen, die einem Namen zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="521fc-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="521fc-119">Nachrichten können an alle Verbindungen in einer Gruppe gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="521fc-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="521fc-120">Gruppen sind die empfohlene Vorgehensweise zum Senden an eine Verbindung oder mehrere Verbindungen, da die Gruppen von der Anwendung verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="521fc-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="521fc-121">Eine Verbindung kann Mitglied mehrerer Gruppen sein.</span><span class="sxs-lookup"><span data-stu-id="521fc-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="521fc-122">Dadurch eignen sich Gruppen ideal für eine Chat-Anwendung, bei der jeder Raum als Gruppe dargestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="521fc-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="521fc-123">Verbindungen können über die Methoden `AddToGroupAsync` und `RemoveFromGroupAsync` zu Gruppen hinzugefügt oder aus diesen entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="521fc-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="521fc-124">Die Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="521fc-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="521fc-125">Die Verbindung muss der Gruppe erneut beitreten, wenn Sie wieder hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="521fc-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="521fc-126">Es ist nicht möglich, die Mitglieder einer Gruppe zu zählen, da diese Informationen nicht verfügbar sind, wenn die Anwendung auf mehrere Server skaliert wird.</span><span class="sxs-lookup"><span data-stu-id="521fc-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="521fc-127">Zum Schutz des Zugriffs auf Ressourcen bei der Verwendung von Gruppen verwenden Sie die [Authentifizierungs-und Autorisierungs](xref:signalr/authn-and-authz) Funktionen in ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="521fc-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="521fc-128">Wenn Sie nur Benutzer zu einer Gruppe hinzufügen, wenn die Anmelde Informationen für diese Gruppe gültig sind, werden an diese Gruppe gesendete Nachrichten nur an autorisierte Benutzer übermittelt.</span><span class="sxs-lookup"><span data-stu-id="521fc-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="521fc-129">Allerdings handelt es sich bei Gruppen nicht um eine Sicherheitsfunktion.</span><span class="sxs-lookup"><span data-stu-id="521fc-129">However, groups are not a security feature.</span></span> <span data-ttu-id="521fc-130">Authentifizierungs Ansprüche verfügen über Funktionen, die von Gruppen nicht unterliegen, z. b. Ablauf und Sperrung.</span><span class="sxs-lookup"><span data-stu-id="521fc-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="521fc-131">Wenn die Berechtigung eines Benutzers für den Zugriff auf die Gruppe aufgehoben wird, müssen Sie diese manuell erkennen und aus der Gruppe entfernen.</span><span class="sxs-lookup"><span data-stu-id="521fc-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="521fc-132">Bei Gruppennamen wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="521fc-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="521fc-133">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="521fc-133">Related resources</span></span>

* [<span data-ttu-id="521fc-134">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="521fc-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="521fc-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="521fc-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="521fc-136">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="521fc-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
