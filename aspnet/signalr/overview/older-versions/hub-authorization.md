---
uid: signalr/overview/older-versions/hub-authorization
title: Authentifizierung und Autorisierung für SignalR-Hubs (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: Dieses Thema beschreibt, wie Sie einschränken, welche Benutzer oder Rollen hubmethoden zugreifen können.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: 3d2dfc0e-eac2-4076-a468-325d3d01cc7b
msc.legacyurl: /signalr/overview/older-versions/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7f4a76109111f19dc4381ad01e642afdabade336
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836898"
---
<a name="authentication-and-authorization-for-signalr-hubs-signalr-1x"></a><span data-ttu-id="e8069-103">Authentifizierung und Autorisierung für SignalR-Hubs (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="e8069-103">Authentication and Authorization for SignalR Hubs (SignalR 1.x)</span></span>
====================
<span data-ttu-id="e8069-104">durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e8069-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="e8069-105">Dieses Thema beschreibt, wie Sie einschränken, welche Benutzer oder Rollen hubmethoden zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="e8069-105">This topic describes how to restrict which users or roles can access hub methods.</span></span>


## <a name="overview"></a><span data-ttu-id="e8069-106">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e8069-106">Overview</span></span>

<span data-ttu-id="e8069-107">Dieses Thema enthält folgende Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="e8069-107">This topic contains the following sections:</span></span>

- [<span data-ttu-id="e8069-108">Autorisieren von Attribut</span><span class="sxs-lookup"><span data-stu-id="e8069-108">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="e8069-109">Erzwingen der Authentifizierung für alle hubs</span><span class="sxs-lookup"><span data-stu-id="e8069-109">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="e8069-110">Benutzerdefinierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="e8069-110">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="e8069-111">Übergeben von Informationen für die Authentifizierung für clients</span><span class="sxs-lookup"><span data-stu-id="e8069-111">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="e8069-112">Authentifizierungsoptionen für .NET-Clients</span><span class="sxs-lookup"><span data-stu-id="e8069-112">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="e8069-113">Cookie mit der Formularauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="e8069-113">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="e8069-114">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e8069-114">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="e8069-115">Connection-header</span><span class="sxs-lookup"><span data-stu-id="e8069-115">Connection header</span></span>](#header)
    - [<span data-ttu-id="e8069-116">Zertifikat</span><span class="sxs-lookup"><span data-stu-id="e8069-116">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="e8069-117">Autorisieren von Attribut</span><span class="sxs-lookup"><span data-stu-id="e8069-117">Authorize attribute</span></span>

<span data-ttu-id="e8069-118">SignalR stellt die [autorisieren](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) Attribut, um anzugeben, welche Benutzer oder Rollen mit einem Hub oder die Methode zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="e8069-118">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="e8069-119">Dieses Attribut befindet sich in der `Microsoft.AspNet.SignalR` Namespace.</span><span class="sxs-lookup"><span data-stu-id="e8069-119">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="e8069-120">Sie wenden die `Authorize` Attribut entweder einen Hub oder bestimmte Methoden in einem Hub.</span><span class="sxs-lookup"><span data-stu-id="e8069-120">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="e8069-121">Beim Anwenden der `Authorize` Attribut, um eine hubklasse, die angegebene autorisierungsanforderung gilt für alle Methoden in den Hub.</span><span class="sxs-lookup"><span data-stu-id="e8069-121">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="e8069-122">Die verschiedenen Typen von autorisierungsanforderungen, die Sie anwenden können, sind unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e8069-122">The different types of authorization requirements that you can apply are shown below.</span></span> <span data-ttu-id="e8069-123">Ohne die `Authorize` -Attribut, die alle öffentliche Methoden für den Hub werden auf einem Client, der mit dem Hub verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="e8069-123">Without the `Authorize` attribute, all public methods on the hub are available to a client that is connected to the hub.</span></span>

<span data-ttu-id="e8069-124">Wenn Sie eine Rolle mit dem Namen "Administrator" in Ihrer Webanwendung definiert haben, können Sie angeben, dass nur Benutzer in dieser Rolle einen Hub mit den folgenden Code zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="e8069-124">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="e8069-125">Sie können auch angeben, dass ein Hub enthält eine Methode, die für alle Benutzer verfügbar ist, und eine zweite Methode, die nur für authentifizierte Benutzer verfügbar ist, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e8069-125">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="e8069-126">Die folgenden Beispiele beziehen sich auf verschiedenen autorisierungsszenarien:</span><span class="sxs-lookup"><span data-stu-id="e8069-126">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="e8069-127">`[Authorize]` – nur authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="e8069-127">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="e8069-128">`[Authorize(Roles = "Admin,Manager")]` – nur authentifizierte Benutzer in den angegebenen Rollen</span><span class="sxs-lookup"><span data-stu-id="e8069-128">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="e8069-129">`[Authorize(Users = "user1,user2")]` – nur authentifizierte Benutzer mit den angegebenen Benutzernamen</span><span class="sxs-lookup"><span data-stu-id="e8069-129">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="e8069-130">`[Authorize(RequireOutgoing=false)]` – nur authentifizierte Benutzer können den Hub aufrufen, aber die Aufrufe vom Server an Clients keine Autorisierung, z. B., Beschränkung Wenn nur bestimmte Benutzer eine Nachricht senden können, aber alle anderen können die Nachricht empfangen.</span><span class="sxs-lookup"><span data-stu-id="e8069-130">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="e8069-131">Die RequireOutgoing-Eigenschaft kann nur für die gesamte Hub-Instanz nicht für Einzelpersonen-Methoden in den Hub angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="e8069-131">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="e8069-132">Wenn es sich bei RequireOutgoing nicht auf "false" festgelegt ist, werden nur Benutzer, die die autorisierungsanforderung erfüllen vom Server aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="e8069-132">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="e8069-133">Erzwingen der Authentifizierung für alle hubs</span><span class="sxs-lookup"><span data-stu-id="e8069-133">Require authentication for all hubs</span></span>

<span data-ttu-id="e8069-134">Sie können erzwingen der Authentifizierung für alle Hubs und hubmethoden in Ihrer Anwendung durch Aufrufen der [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) Methode, wenn die Anwendung gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="e8069-134">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="e8069-135">Sie können diese Methode verwenden, wenn Sie mehrere Hubs haben und eine Anforderung zur Authentifizierung für alle erzwungen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e8069-135">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="e8069-136">Mit dieser Methode können keine Sie-Serverrolle, Benutzer oder ausgehende Autorisierung angeben.</span><span class="sxs-lookup"><span data-stu-id="e8069-136">With this method, you cannot specify role, user, or outgoing authorization.</span></span> <span data-ttu-id="e8069-137">Sie können nur angeben, dass der Zugriff auf die hubmethoden auf authentifizierte Benutzer beschränkt ist.</span><span class="sxs-lookup"><span data-stu-id="e8069-137">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="e8069-138">Allerdings können Sie weiterhin das Authorize-Attribut, Hubs oder Methoden ein, geben Sie zusätzliche Anforderungen anwenden.</span><span class="sxs-lookup"><span data-stu-id="e8069-138">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="e8069-139">Alle Anforderungen, die Sie in Attributen angeben, wird neben der Authentifizierung die grundlegende Anforderung angewendet.</span><span class="sxs-lookup"><span data-stu-id="e8069-139">Any requirement you specify in attributes is applied in addition to the basic requirement of authentication.</span></span>

<span data-ttu-id="e8069-140">Das folgende Beispiel zeigt eine Datei "Global.asax" das alle Methoden des Hubs auf authentifizierte Benutzer beschränkt.</span><span class="sxs-lookup"><span data-stu-id="e8069-140">The following example shows a Global.asax file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="e8069-141">Aufrufen der `RequireAuthentication()` -Methode, nach der Verarbeitung einer Anforderung SignalR, SignalR löst eine `InvalidOperationException` Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="e8069-141">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="e8069-142">Diese Ausnahme wird ausgelöst, da Sie ein Modul auf dem Hubpipeline-Objekt hinzufügen können, nachdem die Pipeline aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="e8069-142">This exception is thrown because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="e8069-143">Im vorherige Beispiel zeigt den Aufruf der `RequireAuthentication` -Methode in der die `Application_Start` Methode, die einmal vor der Verarbeitung der ersten Anforderung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e8069-143">The previous example shows calling the `RequireAuthentication` method in the `Application_Start` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="e8069-144">Benutzerdefinierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="e8069-144">Customized authorization</span></span>

<span data-ttu-id="e8069-145">Bei Bedarf anpassen, wie die Autorisierung bestimmt wird, können Sie eine abgeleitete Klasse erstellen `AuthorizeAttribute` und überschreiben die [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) Methode.</span><span class="sxs-lookup"><span data-stu-id="e8069-145">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="e8069-146">Diese Methode wird aufgerufen, für jede Anforderung zu bestimmen, ob der Benutzer autorisiert ist, um die Anforderung abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="e8069-146">This method is called for each request to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="e8069-147">In der überschriebenen Methode geben Sie die erforderliche Logik an, für Ihr Szenario für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="e8069-147">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="e8069-148">Das folgende Beispiel zeigt, wie Sie das Erzwingen der Autorisierung über anspruchsbasierte Identität.</span><span class="sxs-lookup"><span data-stu-id="e8069-148">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="e8069-149">Übergeben von Informationen für die Authentifizierung für clients</span><span class="sxs-lookup"><span data-stu-id="e8069-149">Pass authentication information to clients</span></span>

<span data-ttu-id="e8069-150">Sie müssen möglicherweise Informationen für die Authentifizierung im Code verwenden, die auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e8069-150">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="e8069-151">Sie übergeben die erforderliche Informationen beim Aufruf der Methoden auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="e8069-151">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="e8069-152">Beispielsweise könnte eine Chat-Anwendung-Methode als Parameter der Benutzername der Person, die eine Nachricht, übergeben wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e8069-152">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="e8069-153">Oder Sie können ein Objekt, um die Authentifizierungsinformationen darstellen, und übermitteln Sie dieses Objekt als Parameter erstellen, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e8069-153">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="e8069-154">Sie sollten niemals eine Clientverbindungs-Id für andere Clients übergeben, wie ein böswilliger Benutzer es verwenden kann, um eine Anforderung von diesem Client imitieren.</span><span class="sxs-lookup"><span data-stu-id="e8069-154">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="e8069-155">Authentifizierungsoptionen für .NET-Clients</span><span class="sxs-lookup"><span data-stu-id="e8069-155">Authentication options for .NET clients</span></span>

<span data-ttu-id="e8069-156">Wenn Sie einen .NET Client, z. B. eine Konsolen-app, die mit einem Hub interagiert, die verfügen authentifizierte Benutzer beschränkt ist. folgende Aktionen ausführen, können Sie die Anmeldeinformationen in ein Cookie, der Connection-Header oder ein Zertifikat für die Authentifizierung übergeben.</span><span class="sxs-lookup"><span data-stu-id="e8069-156">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="e8069-157">In die Beispielen in diesem Abschnitt zeigen, wie die verschiedenen Methoden zum Authentifizieren eines Benutzers verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e8069-157">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="e8069-158">Sie sind nicht voll funktionsfähigen SignalR-apps.</span><span class="sxs-lookup"><span data-stu-id="e8069-158">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="e8069-159">Weitere Informationen zu mit SignalR .NET-Client zu erhalten, finden Sie unter [-API-Leitfaden für die Hubs - Client für .NET](../guide-to-the-api/hubs-api-guide-net-client.md).</span><span class="sxs-lookup"><span data-stu-id="e8069-159">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="e8069-160">Cookie</span><span class="sxs-lookup"><span data-stu-id="e8069-160">Cookie</span></span>

<span data-ttu-id="e8069-161">Wenn der .NET Client mit einem Hub, der ASP.NET-Formularauthentifizierung verwendet interagiert, müssen Sie manuell das Authentifizierungscookie für die Verbindung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e8069-161">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="e8069-162">Sie Cookies, das Hinzufügen der `CookieContainer` Eigenschaft für die [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) Objekt.</span><span class="sxs-lookup"><span data-stu-id="e8069-162">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="e8069-163">Das folgende Beispiel zeigt eine Konsolen-app, die Ruft ein Authentifizierungscookie auf einer Webseite ab und fügt das Cookie für die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="e8069-163">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span> <span data-ttu-id="e8069-164">Die URL `https://www.contoso.com/RemoteLogin` im Beispiel zeigt auf eine Webseite, die Sie erstellen müssen.</span><span class="sxs-lookup"><span data-stu-id="e8069-164">The URL `https://www.contoso.com/RemoteLogin` in the example points to a web page that you would need to create.</span></span> <span data-ttu-id="e8069-165">Die Seite würde abrufen, den bereitgestellten Benutzernamen und das Kennwort, und versuchen, den Benutzer mit den Anmeldeinformationen anzumelden.</span><span class="sxs-lookup"><span data-stu-id="e8069-165">The page would retrieve the posted user name and password, and attempt to log in the user with the credentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="e8069-166">Die Konsolen-app sendet die Anmeldeinformationen für www.contoso.com/RemoteLogin die auf eine leere Seite verweisen kann, die den folgenden Code-Behind-Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="e8069-166">The console app posts the credentials to www.contoso.com/RemoteLogin which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="e8069-167">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e8069-167">Windows authentication</span></span>

<span data-ttu-id="e8069-168">Bei Verwendung der Windows-Authentifizierung können Sie die Anmeldeinformationen des aktuellen Benutzers übergeben, indem Sie mit der [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="e8069-168">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="e8069-169">Sie haben die Anmeldeinformationen für die Verbindung auf den Wert des der DefaultCredentials festlegen.</span><span class="sxs-lookup"><span data-stu-id="e8069-169">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="e8069-170">Connection-header</span><span class="sxs-lookup"><span data-stu-id="e8069-170">Connection header</span></span>

<span data-ttu-id="e8069-171">Wenn Ihre Anwendung keine Cookies verwendet wird, können Sie Benutzerinformationen in der Connection-Header übergeben.</span><span class="sxs-lookup"><span data-stu-id="e8069-171">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="e8069-172">Beispielsweise können Sie ein Token in der Connection-Header übergeben.</span><span class="sxs-lookup"><span data-stu-id="e8069-172">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="e8069-173">Klicken Sie dann würden Sie auf dem Hub das Token des Benutzers überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e8069-173">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="e8069-174">Zertifikat</span><span class="sxs-lookup"><span data-stu-id="e8069-174">Certificate</span></span>

<span data-ttu-id="e8069-175">Sie können ein Clientzertifikat, um zu überprüfen, ob den Benutzer übergeben.</span><span class="sxs-lookup"><span data-stu-id="e8069-175">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="e8069-176">Sie fügen Sie das Zertifikat beim Erstellen der Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8069-176">You add the certificate when creating the connection.</span></span> <span data-ttu-id="e8069-177">Das folgende Beispiel zeigt, wie nur die Verbindung ein Clientzertifikat hinzugefügt wird; die vollständige Konsolen-app werden nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e8069-177">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="e8069-178">Er verwendet den [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) Klasse bietet mehrere Möglichkeiten zum Erstellen des Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="e8069-178">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
