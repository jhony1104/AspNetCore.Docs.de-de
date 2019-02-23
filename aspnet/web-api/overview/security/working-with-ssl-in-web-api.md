---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: Arbeiten mit SSL in Web-API | Microsoft-Dokumentation
author: MikeWasson
description: Veranschaulicht die Verwendung von SSL mit ASP.NET Web-API, einschließlich der Verwendung von SSL-Clientzertifikate.
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744104"
---
<a name="working-with-ssl-in-web-api"></a><span data-ttu-id="db5a4-103">Arbeiten mit SSL in Web-API</span><span class="sxs-lookup"><span data-stu-id="db5a4-103">Working with SSL in Web API</span></span>
====================
<span data-ttu-id="db5a4-104">durch [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="db5a4-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="db5a4-105">Verschiedene häufig verwendete Authentifizierungsschemas sind über einfaches HTTP nicht sicher.</span><span class="sxs-lookup"><span data-stu-id="db5a4-105">Several common authentication schemes are not secure over plain HTTP.</span></span> <span data-ttu-id="db5a4-106">Insbesondere senden die einfache Authentifizierung und Formularauthentifizierung unverschlüsselte Anmeldeinformationen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-106">In particular, Basic authentication and forms authentication send unencrypted credentials.</span></span> <span data-ttu-id="db5a4-107">Sicher, diese Authentifizierungsschemas *müssen* SSL verwenden.</span><span class="sxs-lookup"><span data-stu-id="db5a4-107">To be secure, these authentication schemes *must* use SSL.</span></span> <span data-ttu-id="db5a4-108">Darüber hinaus können der SSL-Clientzertifikate verwendet werden, um Clients zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="db5a4-108">In addition, SSL client certificates can be used to authenticate clients.</span></span>

## <a name="enabling-ssl-on-the-server"></a><span data-ttu-id="db5a4-109">Aktivieren von SSL auf dem Server</span><span class="sxs-lookup"><span data-stu-id="db5a4-109">Enabling SSL on the Server</span></span>

<span data-ttu-id="db5a4-110">So richten Sie SSL in IIS 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="db5a4-110">To set up SSL in IIS 7 or later:</span></span>

- <span data-ttu-id="db5a4-111">Erstellen oder Abrufen eines Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="db5a4-111">Create or get a certificate.</span></span> <span data-ttu-id="db5a4-112">Zu Testzwecken können Sie ein selbstsigniertes Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-112">For testing, you can create a self-signed certificate.</span></span>
- <span data-ttu-id="db5a4-113">Fügen Sie eine HTTPS-Bindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="db5a4-113">Add an HTTPS binding.</span></span>

<span data-ttu-id="db5a4-114">Weitere Informationen finden Sie unter [wie Set Up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span><span class="sxs-lookup"><span data-stu-id="db5a4-114">For details, see [How to Set Up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span></span>

<span data-ttu-id="db5a4-115">Bei lokalen Tests können Sie SSL in IIS Express aus Visual Studio aktivieren.</span><span class="sxs-lookup"><span data-stu-id="db5a4-115">For local testing, you can enable SSL in IIS Express from Visual Studio.</span></span> <span data-ttu-id="db5a4-116">Legen Sie im Eigenschaftenfenster **SSL aktiviert** zu **"true"**.</span><span class="sxs-lookup"><span data-stu-id="db5a4-116">In the Properties window, set **SSL Enabled** to **True**.</span></span> <span data-ttu-id="db5a4-117">Beachten Sie den Wert **SSL-URL**; verwenden Sie diese URL zum Testen von HTTPS-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-117">Note the value of **SSL URL**; use this URL for testing HTTPS connections.</span></span>

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a><span data-ttu-id="db5a4-118">Erzwingen von SSL in einem Web-API-Controller</span><span class="sxs-lookup"><span data-stu-id="db5a4-118">Enforcing SSL in a Web API Controller</span></span>

<span data-ttu-id="db5a4-119">Wenn Sie eine HTTPS und HTTP-Bindung haben, können Clients weiterhin HTTP verwenden, auf die Website zugreifen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-119">If you have both an HTTPS and an HTTP binding, clients can still use HTTP to access the site.</span></span> <span data-ttu-id="db5a4-120">Sie können zulassen, dass einige Ressourcen, die über HTTP verfügbar sein, während andere Ressourcen SSL erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="db5a4-120">You might allow some resources to be available through HTTP, while other resources require SSL.</span></span> <span data-ttu-id="db5a4-121">In diesem Fall verwenden Sie einen Aktionsfilter zur Verwendung von SSL für die geschützten Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-121">In that case, use an action filter to require SSL for the protected resources.</span></span> <span data-ttu-id="db5a4-122">Der folgende Code zeigt eine Web-API-Authentifizierungsfilter, die prüft, ob SSL:</span><span class="sxs-lookup"><span data-stu-id="db5a4-122">The following code shows a Web API authentication filter that checks for SSL:</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

<span data-ttu-id="db5a4-123">Fügen Sie diesen Filter für alle Web-API-Aktionen, die SSL erfordern:</span><span class="sxs-lookup"><span data-stu-id="db5a4-123">Add this filter to any Web API actions that require SSL:</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a><span data-ttu-id="db5a4-124">SSL-Clientzertifikate</span><span class="sxs-lookup"><span data-stu-id="db5a4-124">SSL Client Certificates</span></span>

<span data-ttu-id="db5a4-125">SSL ermöglicht die Authentifizierung mithilfe von Zertifikaten der Public Key-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="db5a4-125">SSL provides authentication by using Public Key Infrastructure certificates.</span></span> <span data-ttu-id="db5a4-126">Der Server muss ein Zertifikat angeben, die der Server an den Client authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="db5a4-126">The server must provide a certificate that authenticates the server to the client.</span></span> <span data-ttu-id="db5a4-127">Es ist weniger gängig ist, für den Client ein Zertifikat mit dem Server bereitstellen, aber dies ist eine Möglichkeit zum Authentifizieren von Clients.</span><span class="sxs-lookup"><span data-stu-id="db5a4-127">It is less common for the client to provide a certificate to the server, but this is one option for authenticating clients.</span></span> <span data-ttu-id="db5a4-128">Um Clientzertifikate mit SSL zu verwenden, benötigen Sie eine Möglichkeit, selbstsignierte Zertifikate für Ihre Benutzer zu verteilen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-128">To use client certificates with SSL, you need a way to distribute signed certificates to your users.</span></span> <span data-ttu-id="db5a4-129">Bei vielen Anwendungstypen dies keine gute benutzererfahrung, aber in einigen Umgebungen (z. B. Enterprise) kann es möglich sein.</span><span class="sxs-lookup"><span data-stu-id="db5a4-129">For many application types, this will not be a good user experience, but in some environments (for example, enterprise) it may be feasible.</span></span>

| <span data-ttu-id="db5a4-130">Vorteile</span><span class="sxs-lookup"><span data-stu-id="db5a4-130">Advantages</span></span> | <span data-ttu-id="db5a4-131">Nachteile</span><span class="sxs-lookup"><span data-stu-id="db5a4-131">Disadvantages</span></span> |
| --- | --- |
| <span data-ttu-id="db5a4-132">-Zertifikats-anmeldeinformationseinstellungen sind stärker als Benutzername und Kennwort.</span><span class="sxs-lookup"><span data-stu-id="db5a4-132">- Certificate credentials are stronger than username/password.</span></span> <span data-ttu-id="db5a4-133">-SSL bietet es sich um einen vollständigen sicheren Kanal, Authentifizierung, Nachrichtenintegrität und die Verschlüsselung von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="db5a4-133">- SSL provides a complete secure channel, with authentication, message integrity, and message encryption.</span></span> | <span data-ttu-id="db5a4-134">– Sie müssen die abrufen und Verwalten von PKI-Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="db5a4-134">- You must obtain and manage PKI certificates.</span></span> <span data-ttu-id="db5a4-135">-Die-Clientplattform muss SSL-Clientzertifikate unterstützen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-135">- The client platform must support SSL client certificates.</span></span> |

<span data-ttu-id="db5a4-136">Um IIS für die Clientzertifikate akzeptieren konfigurieren zu können, öffnen Sie IIS-Manager aus, und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="db5a4-136">To configure IIS to accept client certificates, open IIS Manager and perform the following steps:</span></span>

1. <span data-ttu-id="db5a4-137">Klicken Sie auf den Knoten "Site" in der Strukturansicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="db5a4-137">Click the site node in the tree view.</span></span>
2. <span data-ttu-id="db5a4-138">Doppelklicken Sie auf die **SSL-Einstellungen** -Funktion in der Mitte.</span><span class="sxs-lookup"><span data-stu-id="db5a4-138">Double-click the **SSL Settings** feature in the middle pane.</span></span>
3. <span data-ttu-id="db5a4-139">Klicken Sie unter **Clientzertifikate**, wählen Sie eine der folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="db5a4-139">Under **Client Certificates**, select one of these options:</span></span> 

    - <span data-ttu-id="db5a4-140">**Akzeptieren Sie**: IIS wird ein Zertifikat vom Client akzeptiert, aber es ist nicht erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="db5a4-140">**Accept**: IIS will accept a certificate from the client, but does not require one.</span></span>
    - <span data-ttu-id="db5a4-141">**Erfordern**: Benötigen Sie ein Clientzertifikat.</span><span class="sxs-lookup"><span data-stu-id="db5a4-141">**Require**: Require a client certificate.</span></span> <span data-ttu-id="db5a4-142">(Wenn Sie diese Option aktivieren, müssen Sie auch "SSL erforderlich" auswählen)</span><span class="sxs-lookup"><span data-stu-id="db5a4-142">(To enable this option, you must also select "Require SSL")</span></span>

<span data-ttu-id="db5a4-143">Sie können auch diese Optionen in der Datei "applicationHost.config" festlegen:</span><span class="sxs-lookup"><span data-stu-id="db5a4-143">You can also set these options in the ApplicationHost.config file:</span></span>

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

<span data-ttu-id="db5a4-144">Die **SslNegotiateCert** Flag bedeutet, dass IIS ein Zertifikat vom Client akzeptiert, aber es ist nicht erforderlich sind (entspricht der Option "Accept" im IIS-Manager).</span><span class="sxs-lookup"><span data-stu-id="db5a4-144">The **SslNegotiateCert** flag means IIS will accept a certificate from the client, but does not require one (equivalent to the "Accept" option in IIS Manager).</span></span> <span data-ttu-id="db5a4-145">Um ein Zertifikat anzufordern, legen Sie die **SslRequireCert** Flag.</span><span class="sxs-lookup"><span data-stu-id="db5a4-145">To require a certificate, set the **SslRequireCert** flag.</span></span> <span data-ttu-id="db5a4-146">Zu Testzwecken können Sie auch diese Optionen in IIS Express in der lokalen Applicationhost festlegen. Config-Datei befindet sich in "Documents\IISExpress\config".</span><span class="sxs-lookup"><span data-stu-id="db5a4-146">For testing, you can also set these options in IIS Express, in the local applicationhost.Config file, located in "Documents\IISExpress\config".</span></span>

### <a name="creating-a-client-certificate-for-testing"></a><span data-ttu-id="db5a4-147">Erstellen ein Clientzertifikat für das Testen</span><span class="sxs-lookup"><span data-stu-id="db5a4-147">Creating a Client Certificate for Testing</span></span>

<span data-ttu-id="db5a4-148">Zu Testzwecken können Sie [MakeCert.exe](/windows/desktop/SecCrypto/makecert) ein Clientzertifikat zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="db5a4-148">For testing purposes, you can use [MakeCert.exe](/windows/desktop/SecCrypto/makecert) to create a client certificate.</span></span> <span data-ttu-id="db5a4-149">Erstellen Sie zunächst eine Test-Stammzertifizierungsstelle:</span><span class="sxs-lookup"><span data-stu-id="db5a4-149">First, create a test root authority:</span></span>

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

<span data-ttu-id="db5a4-150">"MakeCert" fordert Sie zur Eingabe eines Kennworts für den privaten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="db5a4-150">Makecert will prompt you to enter a password for the private key.</span></span>

<span data-ttu-id="db5a4-151">Fügen Sie das Zertifikat mit dem Test, die des Servers "Vertrauenswürdige Stammzertifizierungsstellen", wie folgt speichern:</span><span class="sxs-lookup"><span data-stu-id="db5a4-151">Next, add the certificate to the test server's "Trusted Root Certification Authorities" store, as follows:</span></span>

1. <span data-ttu-id="db5a4-152">Öffnen Sie MMC.</span><span class="sxs-lookup"><span data-stu-id="db5a4-152">Open MMC.</span></span>
2. <span data-ttu-id="db5a4-153">Klicken Sie unter **Datei**Option **Snap-In hinzufügen/entfernen**.</span><span class="sxs-lookup"><span data-stu-id="db5a4-153">Under **File**, select **Add/Remove Snap-In**.</span></span>
3. <span data-ttu-id="db5a4-154">Wählen Sie **Computerkonto**.</span><span class="sxs-lookup"><span data-stu-id="db5a4-154">Select **Computer Account**.</span></span>
4. <span data-ttu-id="db5a4-155">Wählen Sie **lokalen Computer** und Abschließen des Assistenten.</span><span class="sxs-lookup"><span data-stu-id="db5a4-155">Select **Local computer** and complete the wizard.</span></span>
5. <span data-ttu-id="db5a4-156">Erweitern Sie im Navigationsbereich "Vertrauenswürdige Stammzertifizierungsstellen".</span><span class="sxs-lookup"><span data-stu-id="db5a4-156">Under the navigation pane, expand the "Trusted Root Certification Authorities" node.</span></span>
6. <span data-ttu-id="db5a4-157">Auf der **Aktion** Startmenü **alle Aufgaben**, und klicken Sie dann auf **Import** um den Zertifikatimport-Assistenten zu starten.</span><span class="sxs-lookup"><span data-stu-id="db5a4-157">On the **Action** menu, point to **All Tasks**, and then click **Import** to start the Certificate Import Wizard.</span></span>
7. <span data-ttu-id="db5a4-158">Navigieren Sie zu der Zertifikatdatei, die Datei TempCA.cer.</span><span class="sxs-lookup"><span data-stu-id="db5a4-158">Browse to the certificate file, TempCA.cer.</span></span>
8. <span data-ttu-id="db5a4-159">Klicken Sie auf **öffnen**, klicken Sie dann auf **Weiter** und Abschließen des Assistenten.</span><span class="sxs-lookup"><span data-stu-id="db5a4-159">Click **Open**, then click **Next** and complete the wizard.</span></span> <span data-ttu-id="db5a4-160">(Sie werden aufgefordert werden, um das Kennwort erneut eingeben.)</span><span class="sxs-lookup"><span data-stu-id="db5a4-160">(You will be prompted to re-enter the password.)</span></span>

<span data-ttu-id="db5a4-161">Nun erstellen Sie ein Clientzertifikat, das durch das erste Zertifikat signiert ist:</span><span class="sxs-lookup"><span data-stu-id="db5a4-161">Now create a client certificate that is signed by the first certificate:</span></span>

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a><span data-ttu-id="db5a4-162">Mithilfe von Clientzertifikaten in Web-API</span><span class="sxs-lookup"><span data-stu-id="db5a4-162">Using Client Certificates in Web API</span></span>

<span data-ttu-id="db5a4-163">Sie können das Clientzertifikat auf dem Server, abrufen, durch den Aufruf [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) für die Anforderungsnachricht.</span><span class="sxs-lookup"><span data-stu-id="db5a4-163">On the server side, you can get the client certificate by calling [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) on the request message.</span></span> <span data-ttu-id="db5a4-164">Die Methode gibt null, wenn kein Clientzertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="db5a4-164">The method returns null if there is no client certificate.</span></span> <span data-ttu-id="db5a4-165">Andernfalls wird ein **X509Certificate2** Instanz.</span><span class="sxs-lookup"><span data-stu-id="db5a4-165">Otherwise, it returns an **X509Certificate2** instance.</span></span> <span data-ttu-id="db5a4-166">Verwenden Sie dieses Objekt zum Abrufen von Informationen aus dem Zertifikat, wie z. B. der Aussteller und Antragsteller.</span><span class="sxs-lookup"><span data-stu-id="db5a4-166">Use this object to get information from the certificate, such as the issuer and subject.</span></span> <span data-ttu-id="db5a4-167">Anschließend können Sie diese Informationen für die Authentifizierung und/oder Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="db5a4-167">Then you can use this information for authentication and/or authorization.</span></span>

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
