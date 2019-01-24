---
uid: signalr/overview/security/persistent-connection-authorization
title: Authentifizierung und Autorisierung für permanente SignalR-Verbindungen | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird. Allgemeine Informationen zum Integrieren von Sicherheit in einer SignalR-Anwendung...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835621"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a><span data-ttu-id="7df20-104">Authentifizierung und Autorisierung für permanente SignalR-Verbindungen</span><span class="sxs-lookup"><span data-stu-id="7df20-104">Authentication and Authorization for SignalR Persistent Connections</span></span>
====================
<span data-ttu-id="7df20-105">durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="7df20-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="7df20-106">In diesem Thema wird beschrieben, wie Autorisierung auf eine permanente Verbindung erzwungen wird.</span><span class="sxs-lookup"><span data-stu-id="7df20-106">This topic describes how to enforce authorization on a persistent connection.</span></span> <span data-ttu-id="7df20-107">Allgemeine Informationen über die Sicherheit in einer SignalR-Anwendung integrieren, finden Sie unter [Einführung zur Sicherheit](introduction-to-security.md).</span><span class="sxs-lookup"><span data-stu-id="7df20-107">For general information about integrating security into a SignalR application, see [Introduction to Security](introduction-to-security.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="7df20-108">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="7df20-108">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="7df20-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7df20-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7df20-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7df20-110">.NET 4.5</span></span>
> - <span data-ttu-id="7df20-111">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="7df20-111">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="7df20-112">Vorherige Versionen dieses Themas</span><span class="sxs-lookup"><span data-stu-id="7df20-112">Previous versions of this topic</span></span>
>
> <span data-ttu-id="7df20-113">Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="7df20-113">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7df20-114">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="7df20-114">Questions and comments</span></span>
>
> <span data-ttu-id="7df20-115">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="7df20-115">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7df20-116">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="7df20-116">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="enforce-authorization"></a><span data-ttu-id="7df20-117">Erzwingen der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7df20-117">Enforce authorization</span></span>

<span data-ttu-id="7df20-118">Autorisierungsregeln zu erzwingen, wenn es sich bei Verwendung einer [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) müssen Sie überschreiben die `AuthorizeRequest` Methode.</span><span class="sxs-lookup"><span data-stu-id="7df20-118">To enforce authorization rules when using a [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="7df20-119">Sie können keine der `Authorize` Attribut mit permanenten Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="7df20-119">You cannot use the `Authorize` attribute with persistent connections.</span></span> <span data-ttu-id="7df20-120">Die `AuthorizeRequest` Methode wird aufgerufen, durch das SignalR Framework vor jeder Anforderung, um sicherzustellen, dass der Benutzer zum Ausführen der angeforderten Aktion berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="7df20-120">The `AuthorizeRequest` method is called by the SignalR Framework before every request to verify that the user is authorized to perform the requested action.</span></span> <span data-ttu-id="7df20-121">Die `AuthorizeRequest` Methode wird nicht vom Client aufgerufen; stattdessen authentifiziert den Benutzer über Standardauthentifizierungsmechanismus Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="7df20-121">The `AuthorizeRequest` method is not called from the client; instead, you authenticate the user through your application's standard authentication mechanism.</span></span>

<span data-ttu-id="7df20-122">Das folgende Beispiel zeigt, wie Sie Anforderungen an authentifizierte Benutzer beschränken.</span><span class="sxs-lookup"><span data-stu-id="7df20-122">The example below shows how to limit requests to authenticated users.</span></span>

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

<span data-ttu-id="7df20-123">Sie können eine beliebige benutzerdefinierte Autorisierungslogik in der Methode AuthorizeRequest hinzufügen; Es wird z. B. überprüft, ob ein Benutzer zu einer bestimmten Rolle gehört.</span><span class="sxs-lookup"><span data-stu-id="7df20-123">You can add any customized authorization logic in the AuthorizeRequest method; such as, checking whether a user belongs to a particular role.</span></span>
