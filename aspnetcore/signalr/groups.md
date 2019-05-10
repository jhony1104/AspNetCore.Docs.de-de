---
title: Verwalten von Benutzern und Gruppen in SignalR
author: bradygaster
description: Übersicht über ASP.NET Core SignalR Benutzer-und Gruppenverwaltung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 180f8b4551eea39cc340bf1d250f4575cb5f71ed
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087433"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="72bed-103">Verwalten von Benutzern und Gruppen in SignalR</span><span class="sxs-lookup"><span data-stu-id="72bed-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="72bed-104">Durch [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="72bed-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="72bed-105">SignalR ermöglicht, Nachrichten an alle Verbindungen, die einen bestimmten Benutzer zugeordneten gesendet werden, sowie um benannte Gruppen von Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="72bed-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="72bed-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="72bed-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="72bed-107">Benutzer in SignalR</span><span class="sxs-lookup"><span data-stu-id="72bed-107">Users in SignalR</span></span>

<span data-ttu-id="72bed-108">SignalR können Sie zum Senden von Nachrichten für alle Verbindungen, die einen bestimmten Benutzer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="72bed-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="72bed-109">SignalR verwendet standardmäßig die `ClaimTypes.NameIdentifier` aus der `ClaimsPrincipal` der Verbindung als die Benutzer-ID zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="72bed-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="72bed-110">Ein einzelner Benutzer kann mehrere Verbindungen mit einer SignalR-app haben.</span><span class="sxs-lookup"><span data-stu-id="72bed-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="72bed-111">Beispielsweise kann ein Benutzer auf ihrem Desktop als auch ihre Rufnummer bestehen.</span><span class="sxs-lookup"><span data-stu-id="72bed-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="72bed-112">Jedes Gerät verfügt über eine separate SignalR-Verbindung, aber sie alle mit demselben Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="72bed-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="72bed-113">Wenn eine Nachricht an den Benutzer gesendet wird, empfangen alle Verbindungen mit diesem Benutzer verknüpften der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="72bed-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="72bed-114">Die Benutzer-ID für eine Verbindung zugegriffen werden kann, indem die `Context.UserIdentifier` Eigenschaft in Ihrem Hub.</span><span class="sxs-lookup"><span data-stu-id="72bed-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="72bed-115">Senden einer Nachricht an einen bestimmten Benutzer durch die Benutzer-ID zum Übergeben der `User` Funktion in der hubmethode, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="72bed-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="72bed-116">Die Benutzer-ID wird Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="72bed-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="72bed-117">Gruppen in SignalR</span><span class="sxs-lookup"><span data-stu-id="72bed-117">Groups in SignalR</span></span>

<span data-ttu-id="72bed-118">Eine Gruppe ist eine Auflistung von Verbindungen mit einem Namen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="72bed-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="72bed-119">Nachrichten können für alle Verbindungen in einer Gruppe gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="72bed-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="72bed-120">Gruppen sind die empfohlene Vorgehensweise, um eine Verbindung oder mehrere Verbindungen gesendet werden, da es sich bei die Gruppen von der Anwendung verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="72bed-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="72bed-121">Eine Verbindung kann Mitglied mehrerer Gruppen sein.</span><span class="sxs-lookup"><span data-stu-id="72bed-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="72bed-122">Dadurch ist Gruppen ideal für Anwendungen wie eine Chat-Anwendung, in dem jeweiligen Raums kann als eine Gruppe dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="72bed-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="72bed-123">Verbindungen hinzugefügt oder aus Gruppen über entfernt werden können, die `AddToGroupAsync` und `RemoveFromGroupAsync` Methoden.</span><span class="sxs-lookup"><span data-stu-id="72bed-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="72bed-124">Der Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt.</span><span class="sxs-lookup"><span data-stu-id="72bed-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="72bed-125">Die Verbindung muss die Gruppe erneut beitreten, wenn er erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="72bed-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="72bed-126">Es ist nicht möglich, um die Mitglieder einer Gruppe zu zählen, da diese Informationen sind nicht verfügbar, wenn die Anwendung auf mehreren Servern skaliert wird.</span><span class="sxs-lookup"><span data-stu-id="72bed-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="72bed-127">Verwenden, um den Zugriff auf Ressourcen zu schützen, bei der Verwendung von Gruppen [Authentifizierung und Autorisierung](xref:signalr/authn-and-authz) Funktionen in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72bed-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="72bed-128">Wenn Sie nur Benutzer zu einer Gruppe hinzufügen, wenn die Anmeldeinformationen für diese Gruppe gültig sind, werden Nachrichten an diese Gruppe nur auf autorisierte Benutzer geleitet.</span><span class="sxs-lookup"><span data-stu-id="72bed-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="72bed-129">Gruppen sind jedoch nicht über eine Sicherheitsfunktion.</span><span class="sxs-lookup"><span data-stu-id="72bed-129">However, groups are not a security feature.</span></span> <span data-ttu-id="72bed-130">Authentifizierungsansprüchen haben Funktionen, die Gruppen nicht, wie z. B. Ablauf und Sperrung sind.</span><span class="sxs-lookup"><span data-stu-id="72bed-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="72bed-131">Wenn die Berechtigung eines Benutzers auf die Gruppe aufgehoben wird, müssen Sie manuell zu erkennen, und entfernen sie aus der Gruppe ein.</span><span class="sxs-lookup"><span data-stu-id="72bed-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="72bed-132">Gruppennamen werden Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="72bed-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="72bed-133">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="72bed-133">Related resources</span></span>

* [<span data-ttu-id="72bed-134">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="72bed-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="72bed-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="72bed-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="72bed-136">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="72bed-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
