---
uid: ajax/cdn/overview
title: Microsoft Ajax Content Delivery Network | Microsoft-Dokumentation
author: rick-anderson
description: ''
ms.author: riande
ms.date: 10/14/2017
ms.assetid: 8935bf14-ca6d-4a4e-9dbe-b96ce74cef49
msc.legacyurl: /ajax/cdn
msc.type: content
ms.openlocfilehash: 2445d788df8ae80ccaad2dfeb19d1ccb91954d40
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249580"
---
<a name="microsoft-ajax-content-delivery-network"></a><span data-ttu-id="7dcfa-102">Microsoft Ajax Content Delivery Network</span><span class="sxs-lookup"><span data-stu-id="7dcfa-102">Microsoft Ajax Content Delivery Network</span></span>
====================
> [!WARNING]
> <span data-ttu-id="7dcfa-103">Produktionsanwendungen dauert eine harte Abhängigkeit nicht auf den CDN-Assets.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-103">Production applications should not take a hard dependency on CDN assets.</span></span> <span data-ttu-id="7dcfa-104">Anwendungen sollten Testen des CDN-Assets, die auf die verwiesen wird, und verwenden ein fallback-Medienobjekt aus, wenn das CDN nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-104">Applications should test for the CDN asset referenced, and use a fallback asset when the CDN is not available.</span></span> 
>
> <span data-ttu-id="7dcfa-105">Das Microsoft Ajax CDN verfügt über keine SLA über ein Azure CDN verwenden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-105">The Microsoft Ajax CDN has no SLA above and beyond using an Azure CDN.</span></span>
>
> <span data-ttu-id="7dcfa-106">Verwendung [GitHub-Problem](https://github.com/aspnet/Docs/issues/5832) zum Melden von Problemen mit der Microsoft Ajax CDN.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-106">Use [this GitHub issue](https://github.com/aspnet/Docs/issues/5832) to report problems with the Microsoft Ajax CDN.</span></span>

## <a name="table-of-contents"></a><span data-ttu-id="7dcfa-107">Inhaltsverzeichnis</span><span class="sxs-lookup"><span data-stu-id="7dcfa-107">Table of Contents</span></span>

<span data-ttu-id="7dcfa-108">**[AJAX.Microsoft.com ajax.aspnetcdn.com umbenannt](#ajaxmicrosoftcom_renamed_to_ajaxaspnetcdncom_18)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-108">**[ajax.microsoft.com renamed to ajax.aspnetcdn.com](#ajaxmicrosoftcom_renamed_to_ajaxaspnetcdncom_18)**</span></span>  
<span data-ttu-id="7dcfa-109">**[Unterstützung von Visual Studio .vsdoc](#Visual_Studio_vsdoc_Support_19)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-109">**[Visual Studio .vsdoc Support](#Visual_Studio_vsdoc_Support_19)**</span></span>  
<span data-ttu-id="7dcfa-110">**[Mithilfe von ASP.NET Ajax über das CDN](#Using_ASPNET_Ajax_from_the_CDN_20)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-110">**[Using ASP.NET Ajax from the CDN](#Using_ASPNET_Ajax_from_the_CDN_20)**</span></span>  
<span data-ttu-id="7dcfa-111">**[Unter Verwendung von jQuery aus dem CDN](#Using_jQuery_from_the_CDN_21)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-111">**[Using jQuery from the CDN](#Using_jQuery_from_the_CDN_21)**</span></span>  
<span data-ttu-id="7dcfa-112">**[Unter Verwendung von jQuery UI aus dem CDN](#Using_jQuery_UI_from_the_CDN_22)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-112">**[Using jQuery UI from the CDN](#Using_jQuery_UI_from_the_CDN_22)**</span></span>  
<span data-ttu-id="7dcfa-113">**[Drittanbieter-Dateien für das CDN](#Third-Party_Files_on_the_CDN_23)**</span><span class="sxs-lookup"><span data-stu-id="7dcfa-113">**[Third-Party Files on the CDN](#Third-Party_Files_on_the_CDN_23)**</span></span>  
  
 [<span data-ttu-id="7dcfa-114">jQuery-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-114">jQuery Releases on the CDN</span></span>](#jQuery_Releases_on_the_CDN_0)  
 [<span data-ttu-id="7dcfa-115">Migrieren von jQuery-Versionen, für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-115">jQuery Migrate Releases on the CDN</span></span>](#jQuery_Migrate_Releases_on_the_CDN_1)  
 [<span data-ttu-id="7dcfa-116">jQuery UI-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-116">jQuery UI Releases on the CDN</span></span>](#jQuery_UI_Releases_on_the_CDN_2)  
 [<span data-ttu-id="7dcfa-117">jQuery-Validierung-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-117">jQuery Validation Releases on the CDN</span></span>](#jQuery_Validation_Releases_on_the_CDN_3)  
 [<span data-ttu-id="7dcfa-118">jQuery Mobile-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-118">jQuery Mobile Releases on the CDN</span></span>](#jQuery_Mobile_Releases_on_the_CDN_4)  
 [<span data-ttu-id="7dcfa-119">jQuery-Vorlagen-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-119">jQuery Templates Releases on the CDN</span></span>](#jQuery_Templates_Releases_on_the_CDN_5)  
 [<span data-ttu-id="7dcfa-120">jQuery-Zyklus-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-120">jQuery Cycle Releases on the CDN</span></span>](#jQuery_Cycle_Releases_on_the_CDN_6)  
 [<span data-ttu-id="7dcfa-121">jQuery-DataTables-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-121">jQuery DataTables Releases on the CDN</span></span>](#jQuery_DataTables_Releases_on_the_CDN_7)  
 [<span data-ttu-id="7dcfa-122">Modernizr-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-122">Modernizr Releases on the CDN</span></span>](#Modernizr_Releases_on_the_CDN_8)  
 [<span data-ttu-id="7dcfa-123">JSHint-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-123">JSHint Releases on the CDN</span></span>](#JSHint_Releases_on_the_CDN_10)  
 [<span data-ttu-id="7dcfa-124">Knockout-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-124">Knockout Releases on the CDN</span></span>](#Knockout_Releases_on_the_CDN_11)  
 [<span data-ttu-id="7dcfa-125">Globalisieren von Releases auf dem CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-125">Globalize Releases on the CDN</span></span>](#Globalize_Releases_on_the_CDN_12)  
 [<span data-ttu-id="7dcfa-126">Reagieren Sie Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-126">Respond Releases on the CDN</span></span>](#Respond_Releases_on_the_CDN_13)  
 [<span data-ttu-id="7dcfa-127">Bootstrap-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-127">Bootstrap Releases on the CDN</span></span>](#Bootstrap_Releases_on_the_CDN_14)  
 [<span data-ttu-id="7dcfa-128">Bootstrap TouchCarousel-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-128">Bootstrap TouchCarousel Releases on the CDN</span></span>](#BootstrapTouchCarousel_Releases_on_the_CDN_18)  
 [<span data-ttu-id="7dcfa-129">Hammer.js-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-129">Hammer.js Releases on the CDN</span></span>](#Hammerjs_Releases_on_the_CDN_19)  
 [<span data-ttu-id="7dcfa-130">ASP.NET Web Forms und Ajax-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-130">ASP.NET Web Forms and Ajax Releases on the CDN</span></span>](#ASPNET_Web_Forms_and_Ajax_Releases_on_the_CDN_15)  
 [<span data-ttu-id="7dcfa-131">ASP.NET MVC-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-131">ASP.NET MVC Releases on the CDN</span></span>](#ASPNET_MVC_Releases_on_the_CDN_16)  
 [<span data-ttu-id="7dcfa-132">ASP.NET SignalR-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-132">ASP.NET SignalR Releases on the CDN</span></span>](#ASPNET_SignalR_Releases_on_the_CDN_17)

<span data-ttu-id="7dcfa-133">Das Microsoft Ajax Content Delivery Network (CDN) hostet beliebten Drittanbieter-JavaScript-Bibliotheken wie jQuery und ermöglicht es Ihnen, die sie ganz einfach Ihre Webanwendungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-133">The Microsoft Ajax Content Delivery Network (CDN) hosts popular third party JavaScript libraries such as jQuery and enables you to easily add them to your Web applications.</span></span> <span data-ttu-id="7dcfa-134">Sie können z. B. starten, unter Verwendung von jQuery die gehostet wird, auf dieses CDN einfach durch Hinzufügen einer &lt;Skript&gt; Tag, um die Seite, die auf ajax.aspnetcdn.com verweist.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-134">For example, you can start using jQuery which is hosted on this CDN simply by adding a &lt;script&gt; tag to your page that points to ajax.aspnetcdn.com.</span></span>

<span data-ttu-id="7dcfa-135">Durch das CDN nutzen, können Sie die Leistung der Ajax-Anwendungen erheblich verbessern.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-135">By taking advantage of the CDN, you can significantly improve the performance of your Ajax applications.</span></span> <span data-ttu-id="7dcfa-136">Der Inhalt des CDN werden auf Servern, die auf der ganzen Welt zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-136">The contents of the CDN are cached on servers located around the world.</span></span> <span data-ttu-id="7dcfa-137">Darüber hinaus kann das CDN Browsern Wiederverwenden von zwischengespeicherten Drittanbieter-JavaScript-Dateien für Websites, die in verschiedenen Domänen befinden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-137">In addition, the CDN enables browsers to reuse cached third party JavaScript files for web sites that are located in different domains.</span></span>

<span data-ttu-id="7dcfa-138">Das CDN unterstützt SSL (HTTPS) an, für den Fall, dass Sie einer Webseite mithilfe des Secure Sockets Layers bereitstellen müssen.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-138">The CDN supports SSL (HTTPS) in case you need to serve a web page using the Secure Sockets Layer.</span></span>

<span data-ttu-id="7dcfa-139">Das CDN hostet die folgenden Drittanbieter-Skriptbibliotheken, die hochgeladen wurden, und für Sie lizenziert sind, die von den Besitzern dieser Bibliotheken:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-139">The CDN hosts the following third party script libraries which have been uploaded, and are licensed to you, by the owners of those libraries:</span></span>

- <span data-ttu-id="7dcfa-140">jQuery (www.jquery.com)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-140">jQuery (www.jquery.com)</span></span>
- <span data-ttu-id="7dcfa-141">jQuery UI (www.jqueryui.com)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-141">jQuery UI (www.jqueryui.com)</span></span>
- <span data-ttu-id="7dcfa-142">jQuery Mobile (www.jquerymobile.com)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-142">jQuery Mobile (www.jquerymobile.com)</span></span>
- <span data-ttu-id="7dcfa-143">jQuery-Validierung (www.jquery.com)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-143">jQuery Validation (www.jquery.com)</span></span>
- <span data-ttu-id="7dcfa-144">jQuery-Zyklus (www.malsup.com/jquery/cycle/)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-144">jQuery Cycle (www.malsup.com/jquery/cycle/)</span></span>
- <span data-ttu-id="7dcfa-145">jQuery DataTables (http://datatables.net/)</span><span class="sxs-lookup"><span data-stu-id="7dcfa-145">jQuery DataTables (http://datatables.net/)</span></span>

<span data-ttu-id="7dcfa-146">Das Microsoft Ajax CDN umfasst auch die folgenden Bibliotheken, die von Microsoft hochgeladen wurden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-146">The Microsoft Ajax CDN also includes the following libraries which have been uploaded by Microsoft:</span></span>

- <span data-ttu-id="7dcfa-147">ASP.NET Ajax</span><span class="sxs-lookup"><span data-stu-id="7dcfa-147">ASP.NET Ajax</span></span>
- <span data-ttu-id="7dcfa-148">ASP.NET MVC-JavaScript-Dateien</span><span class="sxs-lookup"><span data-stu-id="7dcfa-148">ASP.NET MVC JavaScript Files</span></span>
- <span data-ttu-id="7dcfa-149">ASP.NET SignalR JavaScript-Dateien</span><span class="sxs-lookup"><span data-stu-id="7dcfa-149">ASP.NET SignalR JavaScript Files</span></span>

<span data-ttu-id="7dcfa-150">Microsoft beansprucht nicht den Besitz von Drittanbieter Bibliotheken, die auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-150">Microsoft does not claim ownership of any third-party libraries hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-151">Den Urheberrechtsinhabern Bibliotheken sind diese Bibliotheken Ihnen Lizenzierung.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-151">The copyright owners of the libraries are licensing these libraries to you.</span></span> <span data-ttu-id="7dcfa-152">Alle Rechte, die Sie möglicherweise herunterladen und verwenden diese Bibliotheken werden ausschließlich von der jeweiligen Urheberrechtsinhabern gewährt.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-152">Any rights that you may have to download and use such libraries are granted solely by the respective copyright owners.</span></span> <span data-ttu-id="7dcfa-153">Da diese sich nicht um Microsoft-Bibliotheken sind, bietet Microsoft keine GEWÄHRLEISTUNGEN oder geistiges Eigentum Rights-Lizenzen (einschließlich keine implizite Patentrechte) für die Drittanbieter-Bibliotheken, die auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-153">Because these are not Microsoft libraries, Microsoft provides no warranties or intellectual property rights licenses (including no implied patent rights) for the third party libraries hosted on this CDN.</span></span>

<span data-ttu-id="7dcfa-154">Wenn Sie Ihre JavaScript-Bibliothek senden möchten, und Ihre Bibliothek eine von der Top-JavaScript-Bibliotheken ist (wie in http://trends.builtwith.com) oder Erweiterungen /-Plug-Ins auf diese Bibliotheken sind (a) beliebte; oder (b) nützlich für die Verwendung in ASP.NET wenden Sie sich an, die AjaxCDNSubmission@Microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-154">If you wish to submit your JavaScript library and your library is one of the top JavaScript libraries (as listed on http://trends.builtwith.com) or extensions/plugins to these libraries that are (a) popular; or (b) helpful for use on ASP.NET then please contact AjaxCDNSubmission@Microsoft.com.</span></span>

<a id="ajaxmicrosoftcom_renamed_to_ajaxaspnetcdncom_18"></a>

## <a name="ajaxmicrosoftcom-renamed-to-ajaxaspnetcdncom"></a><span data-ttu-id="7dcfa-155">AJAX.Microsoft.com ajax.aspnetcdn.com umbenannt</span><span class="sxs-lookup"><span data-stu-id="7dcfa-155">ajax.microsoft.com renamed to ajax.aspnetcdn.com</span></span>

<span data-ttu-id="7dcfa-156">Das CDN verwendet, um den Domänennamen "Microsoft.com" verwenden und wurde geändert, um den Domänennamen aspnetcdn.com verwenden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-156">The CDN used to use the microsoft.com domain name and has been changed to use the aspnetcdn.com domain name.</span></span> <span data-ttu-id="7dcfa-157">Diese Änderung wurde vorgenommen, um die Leistung zu erhöhen, da bei ein Browser auf die Domäne "Microsoft.com" verwiesen wird es alle Cookies aus der Domäne über das Netzwerk mit jeder Anforderung sendet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-157">This change was made to increase performance because when a browser referenced the microsoft.com domain it would send any cookies from that domain across the wire with each request.</span></span> <span data-ttu-id="7dcfa-158">Durch das Umbenennen, die auf einen Domänennamen als "Microsoft.com" kann die Leistung von möglichst auf 25 % erhöht werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-158">By renaming to a domain name other than microsoft.com performance can be increased by as much to 25%.</span></span> <span data-ttu-id="7dcfa-159">Beachten Sie ajax.microsoft.com funktioniert weiterhin ajax.aspnetcdn.com wird jedoch empfohlen.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-159">Note ajax.microsoft.com will continue to function but ajax.aspnetcdn.com is recommended.</span></span>

- <span data-ttu-id="7dcfa-160">Altes Format: https://ajax.microsoft.com/ajax/jQuery/jquery-1.8.0.js</span><span class="sxs-lookup"><span data-stu-id="7dcfa-160">Old Format: https://ajax.microsoft.com/ajax/jQuery/jquery-1.8.0.js</span></span>
- <span data-ttu-id="7dcfa-161">Neues Format: https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.js</span><span class="sxs-lookup"><span data-stu-id="7dcfa-161">New Format: https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.js</span></span>

<a id="Visual_Studio_vsdoc_Support_19"></a>

## <a name="visual-studio-vsdoc-support"></a><span data-ttu-id="7dcfa-162">Unterstützung von Visual Studio .vsdoc</span><span class="sxs-lookup"><span data-stu-id="7dcfa-162">Visual Studio .vsdoc Support</span></span>

<span data-ttu-id="7dcfa-163">.Vsdoc Dateien ordnungsgemäß mit Visual Studio 2008 verwenden, müssen Sie sicherstellen, dass Visual Studio 2008 SP1 ist installiert, und der Hotfix für Vsdoc-Dateien installiert.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-163">To use the .vsdoc files properly with Visual Studio 2008 you need to make sure that you have VS 2008 SP1 installed and the hotfix for vsdoc files installed.</span></span> <span data-ttu-id="7dcfa-164">Sie können diese hier abrufen:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-164">You can get these from here:</span></span>

- [<span data-ttu-id="7dcfa-165">Herunterladen von Visual Studio 2008 SP1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-165">Download Visual Studio 2008 SP1</span></span>](https://www.microsoft.com/downloads/en/details.aspx?FamilyId=FBEE1648-7106-44A7-9649-6D9F6D58056E&amp;displaylang=en "Herunterladen von Visual Studio 2008 SP1")
- [<span data-ttu-id="7dcfa-166">Herunterladen von .vsdoc Hotfix für Visual Studio 2008 SP1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-166">Download .vsdoc hotfix for Visual Studio 2008 SP1</span></span>](https://code.msdn.microsoft.com/KB958502/Release/ProjectReleases.aspx?ReleaseId=1736 ".vsdoc Hotfix für Visual Studio 2008 SP1 herunterladen")

<span data-ttu-id="7dcfa-167">Visual Studio 2010 unterstützt .vsdoc-Dateien ohne zusätzliche Patches.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-167">Visual Studio 2010 supports .vsdoc files without any additional patches.</span></span>

<a id="Using_ASPNET_Ajax_from_the_CDN_20"></a>

## <a name="using-aspnet-ajax-from-the-cdn"></a><span data-ttu-id="7dcfa-168">Mithilfe von ASP.NET Ajax über das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-168">Using ASP.NET Ajax from the CDN</span></span>

<span data-ttu-id="7dcfa-169">Wenn Sie ASP.NET 4 zu verwenden, können Sie alle Anforderungen für ASP.NET Framework-Skripts an das CDN umleiten.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-169">When using ASP.NET 4, you can redirect all requests for ASP.NET framework scripts to the CDN.</span></span> <span data-ttu-id="7dcfa-170">Abrufen der Skripts aus dem CDN anstelle von Ihrem lokalen Webserver kann die Leistung von öffentlichen ASP.NET-Websites erheblich verbessert werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-170">Retrieving scripts from the CDN instead of your local web server can substantially improve the performance of public ASP.NET websites.</span></span>

<span data-ttu-id="7dcfa-171">Verwenden Sie die ScriptManager EnableCDN-Eigenschaft, um alle ASP.NET Framework-Skript-Anforderungen an das Microsoft Ajax CDN umleiten:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-171">Use the ScriptManager EnableCDN property to redirect all ASP.NET framework script requests to the Microsoft Ajax CDN:</span></span>

[!code-aspx[Main](overview/samples/sample1.aspx)]

<a id="Using_jQuery_from_the_CDN_21"></a>

## <a name="using-jquery-from-the-cdn"></a><span data-ttu-id="7dcfa-172">Unter Verwendung von jQuery aus dem CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-172">Using jQuery from the CDN</span></span>

<span data-ttu-id="7dcfa-173">Sie können für CDN in Ihrer Webanwendung durch Hinzufügen der folgenden Script-Element zu einer Seite gehosteten jQuery-Skripts verwenden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-173">You can use jQuery scripts hosted on CDN in your Web application by adding the following script element to a page:</span></span>

[!code-html[Main](overview/samples/sample2.html)]

<span data-ttu-id="7dcfa-174">Das CDN enthält auch die verkleinerte Version des jQuery-Skripts, die Sie abrufen können mit dem folgenden Element:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-174">The CDN also includes the minified version of the jQuery script, which you can get using the following element:</span></span>

[!code-html[Main](overview/samples/sample3.html)]

<span data-ttu-id="7dcfa-175">Damit Ihre Seite auf die jQuery von einem lokalen Pfad auf Ihrer eigenen Website geladen werden, wenn das CDN nicht verfügbar ist, geschieht fallback wird, fügen Sie unmittelbar nach dem Element, das Verweisen auf das CDN das folgende Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-175">To allow your page to fallback to loading jQuery from a local path on your own website if the CDN happens to be unavailable, add the following element immediately after the element referencing the CDN:</span></span>

[!code-html[Main](overview/samples/sample4.html)]

<span data-ttu-id="7dcfa-176">Die folgenden Beispielseite verwendet die CDN-Version der jQuery-Bibliothek (mit Fallback auf eine lokale Kopie) der Inhalt eines Div-Elements angezeigt, wenn auf eine Schaltfläche geklickt wird.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-176">The following sample page uses the CDN version of the jQuery library (with fallback to a local copy) to display the contents of a div element when a button is clicked.</span></span>

[!code-html[Main](overview/samples/sample5.html)]

<span data-ttu-id="7dcfa-177">Sie können erfahren Sie mehr über jQuery und eine lokale Kopie von jQuery herunterzuladen, finden Sie unter den [jQuery](http://jquery.com/) Website.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-177">You can learn more about jQuery and download a local copy of jQuery by visiting the [jQuery](http://jquery.com/) Web site.</span></span>

<a id="Using_jQuery_UI_from_the_CDN_22"></a>

## <a name="using-jquery-ui-from-the-cdn"></a><span data-ttu-id="7dcfa-178">Unter Verwendung von jQuery UI aus dem CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-178">Using jQuery UI from the CDN</span></span>

<span data-ttu-id="7dcfa-179">Das CDN enthält außerdem die jQuery-UI-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-179">The CDN also hosts the jQuery UI library.</span></span> <span data-ttu-id="7dcfa-180">JQuery UI-Bibliothek enthält einen umfangreichen Satz von Widgets und Effekte, die Sie in Ihren ASP.NET-Anwendungen verwenden können.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-180">The jQuery UI library includes a rich set of widgets and effects that you can use in your ASP.NET applications.</span></span> <span data-ttu-id="7dcfa-181">Die folgende Seite wird z. B. veranschaulicht, wie Sie die jQuery UI Datepicker im Kontext einer ASP.NET Web Forms-Anwendung verwenden können, um ein Popupkalender angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-181">For example, the following page illustrates how you can use the jQuery UI Datepicker in the context of an ASP.NET Web Forms application to display a pop-up calendar:</span></span>

[!code-aspx[Main](overview/samples/sample6.aspx)]

<span data-ttu-id="7dcfa-182">Wenn Sie den Fokus auf das Textfeld ein, die mithilfe der Tastatur zu verschieben, wird ein Kalender angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-182">When you move focus to the TextBox using your keyboard, a calendar is displayed:</span></span>

![Popupkalenders erstellt, die durch "DatePicker"](overview/_static/image1.png)

<span data-ttu-id="7dcfa-184">Beachten Sie, dass Sie drei Dateien aus dem CDN im obigen Code enthalten müssen:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-184">Notice that you must include three files from the CDN in the code above:</span></span>

- <span data-ttu-id="7dcfa-185">Die jQuery-Bibliothek &mdash; die jQuery UI-Bibliothek hängt von der jQuery-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-185">The jQuery library &mdash; The jQuery UI library depends on the jQuery library.</span></span> <span data-ttu-id="7dcfa-186">Sie müssen die jQuery-Bibliothek zu Ihrer Seite hinzufügen, bevor Sie die jQuery-UI-Bibliothek hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-186">You must add the jQuery library to your page before you add the jQuery UI library.</span></span>
- <span data-ttu-id="7dcfa-187">JQuery UI-Bibliothek &mdash; die jQuery UI-Bibliothek enthält alle Effekte von jQuery UI und Widgets, wie z. B. das "DatePicker"-Widget auf der Seite, die oben genannten verwendet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-187">The jQuery UI library &mdash; The jQuery UI library contains all of the jQuery UI effects and widgets such as the Datepicker widget used in the page above.</span></span>
- <span data-ttu-id="7dcfa-188">JQuery UI Design &mdash; die jQuery-Benutzeroberfläche unterstützt die verschiedenen Designs.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-188">A jQuery UI theme &mdash; The jQuery UI supports different themes.</span></span> <span data-ttu-id="7dcfa-189">Die Seite enthält einen Link zu einer CSS-Datei, um das Design "Redmond" zu importieren.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-189">The page above includes a link to a CSS file to import the Redmond theme.</span></span>

<span data-ttu-id="7dcfa-190">Alle von den standardmäßigen jQuery UI-Designs werden für das CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-190">All of the standard jQuery UI themes are hosted on the CDN.</span></span> <span data-ttu-id="7dcfa-191">[Besuchen Sie diese Seite](jquery-ui/cdnjqueryui1910.md "jQuery UI 1.8.10 für das Microsoft Ajax CDN") Miniaturansichten für jedes Design an.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-191">[Visit this page](jquery-ui/cdnjqueryui1910.md "jQuery UI 1.8.10 on the Microsoft Ajax CDN") to view thumbnails for each theme.</span></span>

<span data-ttu-id="7dcfa-192">Weitere Informationen zu den jQuery-UI-Bibliothek finden Sie auf der offiziellen [jQuery UI Website](http://jQueryUI.com "jQuery UI Website").</span><span class="sxs-lookup"><span data-stu-id="7dcfa-192">To learn more about the jQuery UI library, visit the official [jQuery UI website](http://jQueryUI.com "jQuery UI website").</span></span>

<a id="Third-Party_Files_on_the_CDN_23"></a>

## <a name="third-party-files-on-the-cdn"></a><span data-ttu-id="7dcfa-193">Drittanbieter-Dateien für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-193">Third-Party Files on the CDN</span></span>

<span data-ttu-id="7dcfa-194">Das CDN hostet einiger der beliebtesten JavaScript-Bibliotheken von Drittanbietern.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-194">The CDN hosts some of the most popular third party JavaScript libraries.</span></span> <span data-ttu-id="7dcfa-195">Microsoft beansprucht nicht den Besitz von Drittanbieter Bibliotheken, die auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-195">Microsoft does not claim ownership of any third-party libraries hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-196">Den Urheberrechtsinhabern Bibliotheken sind diese Bibliotheken Ihnen Lizenzierung.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-196">The copyright owners of the libraries are licensing these libraries to you.</span></span> <span data-ttu-id="7dcfa-197">Alle Rechte, die Sie möglicherweise herunterladen und verwenden diese Bibliotheken werden ausschließlich von der jeweiligen Urheberrechtsinhabern gewährt.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-197">Any rights that you may have to download and use such libraries are granted solely by the respective copyright owners.</span></span> <span data-ttu-id="7dcfa-198">Da diese sich nicht um Microsoft-Bibliotheken sind, bietet Microsoft keine GEWÄHRLEISTUNGEN oder geistiges Eigentum Rights-Lizenzen (einschließlich keine implizite Patentrechte) für die Drittanbieter-Bibliotheken, die auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-198">Because these are not Microsoft libraries, Microsoft provides no warranties or intellectual property rights licenses (including no implied patent rights) for the third party libraries hosted on this CDN.</span></span>

<a id="jQuery_Releases_on_the_CDN_0"></a>

### <a name="jquery-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-199">jQuery-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-199">jQuery Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-200">Die folgenden Versionen von jQuery, die für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-200">The following releases of jQuery are hosted on the CDN:</span></span>

#### <a name="jquery-version-331"></a><span data-ttu-id="7dcfa-201">jQuery-Version 3.3.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-201">jQuery version 3.3.1</span></span>
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.3.1.slim.min.map

#### <a name="jquery-version-321"></a><span data-ttu-id="7dcfa-202">jQuery-Version 3.2.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-202">jQuery version 3.2.1</span></span>
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.1.slim.min.map

#### <a name="jquery-version-320"></a><span data-ttu-id="7dcfa-203">jQuery-Version 3.2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-203">jQuery version 3.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.2.0.slim.min.map

#### <a name="jquery-version-311"></a><span data-ttu-id="7dcfa-204">3.1.1 jQuery-version</span><span class="sxs-lookup"><span data-stu-id="7dcfa-204">jQuery version 3.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.1.slim.min.map

#### <a name="jquery-version-310"></a><span data-ttu-id="7dcfa-205">jQuery-Version 3.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-205">jQuery version 3.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.1.0.slim.min.map

#### <a name="jquery-version-300"></a><span data-ttu-id="7dcfa-206">jQuery-Version 3.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-206">jQuery version 3.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.min.map
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.slim.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.slim.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-3.0.0.slim.min.map

#### <a name="jquery-version-224"></a><span data-ttu-id="7dcfa-207">jQuery-Version 2.2.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-207">jQuery version 2.2.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.4.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.4.min.map

#### <a name="jquery-version-223"></a><span data-ttu-id="7dcfa-208">jQuery-Version 2.2.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-208">jQuery version 2.2.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.3.min.map

#### <a name="jquery-version-222"></a><span data-ttu-id="7dcfa-209">jQuery-Version 2.2.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-209">jQuery version 2.2.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.2.min.map

#### <a name="jquery-version-221"></a><span data-ttu-id="7dcfa-210">jQuery-Version 2.2.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-210">jQuery version 2.2.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.1.min.map

#### <a name="jquery-version-220"></a><span data-ttu-id="7dcfa-211">jQuery-Version 2.2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-211">jQuery version 2.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.2.0.min.map

#### <a name="jquery-version-214"></a><span data-ttu-id="7dcfa-212">jQuery-Version 2.1.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-212">jQuery version 2.1.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.4.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.4.min.map

#### <a name="jquery-version-213"></a><span data-ttu-id="7dcfa-213">jQuery Version 2.1.3 ist</span><span class="sxs-lookup"><span data-stu-id="7dcfa-213">jQuery version 2.1.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.3.min.map

#### <a name="jquery-version-212"></a><span data-ttu-id="7dcfa-214">jQuery-Version 2.1.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-214">jQuery version 2.1.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.2.min.js

#### <a name="jquery-version-211"></a><span data-ttu-id="7dcfa-215">jQuery-Version 2.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-215">jQuery version 2.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.map

#### <a name="jquery-version-210"></a><span data-ttu-id="7dcfa-216">jQuery-Version 2.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-216">jQuery version 2.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.0-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.0.min.map

#### <a name="jquery-version-203"></a><span data-ttu-id="7dcfa-217">jQuery-Version 2.0.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-217">jQuery version 2.0.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.min.map

#### <a name="jquery-version-202"></a><span data-ttu-id="7dcfa-218">jQuery-Version 2.0.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-218">jQuery version 2.0.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.2-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.2.min.map

#### <a name="jquery-version-201"></a><span data-ttu-id="7dcfa-219">jQuery-Version 2.0.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-219">jQuery version 2.0.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.1-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.1.min.map

#### <a name="jquery-version-200"></a><span data-ttu-id="7dcfa-220">jQuery-Version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-220">jQuery version 2.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.0-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.0.min.map

#### <a name="jquery-version-1124"></a><span data-ttu-id="7dcfa-221">jQuery-Version 1.12.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-221">jQuery version 1.12.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.4.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.4.min.map

#### <a name="jquery-version-1123"></a><span data-ttu-id="7dcfa-222">jQuery-Version 1.12.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-222">jQuery version 1.12.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.3.min.map

#### <a name="jquery-version-1122"></a><span data-ttu-id="7dcfa-223">jQuery-Version 1.12.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-223">jQuery version 1.12.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.2.min.map

#### <a name="jquery-version-1121"></a><span data-ttu-id="7dcfa-224">jQuery-Version 1.12.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-224">jQuery version 1.12.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.1.min.map

#### <a name="jquery-version-1120"></a><span data-ttu-id="7dcfa-225">jQuery-Version 1.12.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-225">jQuery version 1.12.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.12.0.min.map

#### <a name="jquery-version-1113"></a><span data-ttu-id="7dcfa-226">jQuery-Version 1.11.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-226">jQuery version 1.11.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.3.min.map

#### <a name="jquery-version-1112"></a><span data-ttu-id="7dcfa-227">jQuery-Version 1.11.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-227">jQuery version 1.11.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.map

#### <a name="jquery-version-1111"></a><span data-ttu-id="7dcfa-228">jQuery-Version 1.11.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-228">jQuery version 1.11.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.1.min.map

#### <a name="jquery-version-1110"></a><span data-ttu-id="7dcfa-229">jQuery-Version 1.11.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-229">jQuery version 1.11.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.0-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.0.min.map

#### <a name="jquery-version-1102"></a><span data-ttu-id="7dcfa-230">jQuery-Version 1.10.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-230">jQuery version 1.10.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.map

#### <a name="jquery-version-1101"></a><span data-ttu-id="7dcfa-231">jQuery-Version 1.10.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-231">jQuery version 1.10.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.1-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.1.min.map

#### <a name="jquery-version-1100"></a><span data-ttu-id="7dcfa-232">jQuery-Version 1.10.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-232">jQuery version 1.10.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.0-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.0.min.map

#### <a name="jquery-version-191"></a><span data-ttu-id="7dcfa-233">1.9.1 für jQuery-version</span><span class="sxs-lookup"><span data-stu-id="7dcfa-233">jQuery version 1.9.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.map

#### <a name="jquery-version-190"></a><span data-ttu-id="7dcfa-234">jQuery-Version 1.9.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-234">jQuery version 1.9.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.0-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.0.min.map

#### <a name="jquery-version-183"></a><span data-ttu-id="7dcfa-235">jQuery 1.8.3-version</span><span class="sxs-lookup"><span data-stu-id="7dcfa-235">jQuery version 1.8.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.3-vsdoc.js

#### <a name="jquery-version-182"></a><span data-ttu-id="7dcfa-236">jQuery version 1.8.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-236">jQuery version 1.8.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.2-vsdoc.js

#### <a name="jquery-version-181"></a><span data-ttu-id="7dcfa-237">jQuery-Version 1.8.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-237">jQuery version 1.8.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.1-vsdoc.js

#### <a name="jquery-version-180"></a><span data-ttu-id="7dcfa-238">jQuery-Version 1.8.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-238">jQuery version 1.8.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0-vsdoc.js

#### <a name="jquery-version-172"></a><span data-ttu-id="7dcfa-239">jQuery-Version 1.7.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-239">jQuery version 1.7.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.2.min.js

#### <a name="jquery-version-171"></a><span data-ttu-id="7dcfa-240">jQuery-Version 1.7.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-240">jQuery version 1.7.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.1-vsdoc.js

#### <a name="jquery-version-17"></a><span data-ttu-id="7dcfa-241">jQuery-Version 1.7</span><span class="sxs-lookup"><span data-stu-id="7dcfa-241">jQuery version 1.7</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7-vsdoc.js

#### <a name="jquery-version-164"></a><span data-ttu-id="7dcfa-242">jQuery-Version 1.6.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-242">jQuery version 1.6.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.4.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.4-vsdoc.js

#### <a name="jquery-version-163"></a><span data-ttu-id="7dcfa-243">jQuery-Version 1.6.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-243">jQuery version 1.6.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.3-vsdoc.js

#### <a name="jquery-version-162"></a><span data-ttu-id="7dcfa-244">jQuery-Version 1.6.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-244">jQuery version 1.6.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.2-vsdoc.js

#### <a name="jquery-version-161"></a><span data-ttu-id="7dcfa-245">jQuery-Version 1.6.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-245">jQuery version 1.6.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.1-vsdoc.js

#### <a name="jquery-version-16"></a><span data-ttu-id="7dcfa-246">jQuery-Version 1.6</span><span class="sxs-lookup"><span data-stu-id="7dcfa-246">jQuery version 1.6</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.6-vsdoc.js

#### <a name="jquery-version-152"></a><span data-ttu-id="7dcfa-247">jQuery version 1.5.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-247">jQuery version 1.5.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.2-vsdoc.js

#### <a name="jquery-version-151"></a><span data-ttu-id="7dcfa-248">jQuery 1.5.1-version</span><span class="sxs-lookup"><span data-stu-id="7dcfa-248">jQuery version 1.5.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.1-vsdoc.js

#### <a name="jquery-version-15"></a><span data-ttu-id="7dcfa-249">jQuery-Version 1.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-249">jQuery version 1.5</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.5-vsdoc.js

#### <a name="jquery-version-144"></a><span data-ttu-id="7dcfa-250">jQuery-Version 1.4.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-250">jQuery version 1.4.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.4.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.4-vsdoc.js

#### <a name="jquery-version-143"></a><span data-ttu-id="7dcfa-251">jQuery-Version 1.4.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-251">jQuery version 1.4.3</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.3.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.3.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.3-vsdoc.js

#### <a name="jquery-version-142"></a><span data-ttu-id="7dcfa-252">jQuery-Version 1.4.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-252">jQuery version 1.4.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.2-vsdoc.js

#### <a name="jquery-version-141"></a><span data-ttu-id="7dcfa-253">jQuery-Version 1.4.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-253">jQuery version 1.4.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.1.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.1.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.1-vsdoc.js

#### <a name="jquery-version-14"></a><span data-ttu-id="7dcfa-254">jQuery-Version 1.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-254">jQuery version 1.4</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.4.min.js

#### <a name="jquery-version-132"></a><span data-ttu-id="7dcfa-255">jQuery-Version 1.3.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-255">jQuery version 1.3.2</span></span>

- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.3.2.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.3.2.min.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.3.2-vsdoc.js
- https://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.3.2.min-vsdoc.js

<a id="jQuery_Migrate_Releases_on_the_CDN_1"></a>

### <a name="jquery-migrate-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-256">Migrieren von jQuery-Versionen, für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-256">jQuery Migrate Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-257">Die folgenden Versionen von jQuery migrieren, die für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-257">The following releases of jQuery Migrate are hosted on the CDN:</span></span>

#### <a name="jquery-migrate-version-300"></a><span data-ttu-id="7dcfa-258">jQuery migrieren Version 3.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-258">jQuery Migrate version 3.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-3.0.0.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-3.0.0.min.js

#### <a name="jquery-migrate-version-121"></a><span data-ttu-id="7dcfa-259">jQuery migrieren Version 1.2.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-259">jQuery Migrate version 1.2.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.2.1.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.2.1.min.js

<span data-ttu-id="7dcfa-260">jQuery-Version 1.2.0-Migration</span><span class="sxs-lookup"><span data-stu-id="7dcfa-260">jQuery Migrate version 1.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.2.0.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.2.0.min.js

#### <a name="jquery-migrate-version-111"></a><span data-ttu-id="7dcfa-261">jQuery-Version 1.1.1-Migration</span><span class="sxs-lookup"><span data-stu-id="7dcfa-261">jQuery Migrate version 1.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.1.1.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.1.1.min.js

#### <a name="jquery-migrate-version-110"></a><span data-ttu-id="7dcfa-262">jQuery-Version 1.1.0-Migration</span><span class="sxs-lookup"><span data-stu-id="7dcfa-262">jQuery Migrate version 1.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.1.0.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.1.0.min.js

#### <a name="jquery-migrate-version-100"></a><span data-ttu-id="7dcfa-263">jQuery-Version 1.0.0-Migration</span><span class="sxs-lookup"><span data-stu-id="7dcfa-263">jQuery Migrate version 1.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.0.0.js
- https://ajax.aspnetcdn.com/ajax/jquery.migrate/jquery-migrate-1.0.0.min.js

<a id="jQuery_UI_Releases_on_the_CDN_2"></a>

### <a name="jquery-ui-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-264">jQuery UI-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-264">jQuery UI Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-265">Die folgenden Versionen der jQuery UI-Bibliothek, die auf dieses CDN gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-265">The following releases of the jQuery UI library are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-266">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-266">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-267">jQuery UI 1.12.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-267">jQuery UI 1.12.1</span></span>](jquery-ui/cdnjqueryui1121.md "jQuery UI 1.12.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-268">jQuery UI 1.12.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-268">jQuery UI 1.12.0</span></span>](jquery-ui/cdnjqueryui1120.md "jQuery UI 1.12.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-269">jQuery UI 1.11.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-269">jQuery UI 1.11.4</span></span>](jquery-ui/cdnjqueryui1114.md "jQuery UI 1.11.4 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-270">jQuery UI 1.11.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-270">jQuery UI 1.11.3</span></span>](jquery-ui/cdnjqueryui1113.md "jQuery UI 1.11.3 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-271">jQuery UI 1.11.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-271">jQuery UI 1.11.2</span></span>](jquery-ui/cdnjqueryui1112.md "jQuery UI 1.11.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-272">jQuery UI 1.11.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-272">jQuery UI 1.11.1</span></span>](jquery-ui/cdnjqueryui1111.md "jQuery UI 1.11.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-273">jQuery UI 1.11.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-273">jQuery UI 1.11.0</span></span>](jquery-ui/cdnjqueryui1110.md "jQuery UI 1.11.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-274">jQuery UI 1.10.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-274">jQuery UI 1.10.4</span></span>](jquery-ui/cdnjqueryui1104.md "jQuery UI 1.10.4 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-275">jQuery UI 1.10.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-275">jQuery UI 1.10.3</span></span>](jquery-ui/cdnjqueryui1103.md "jQuery UI 1.10.3 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-276">jQuery UI 1.10.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-276">jQuery UI 1.10.2</span></span>](jquery-ui/cdnjqueryui1102.md "jQuery UI 1.10.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-277">jQuery UI 1.10.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-277">jQuery UI 1.10.1</span></span>](jquery-ui/cdnjqueryui1101.md "jQuery UI 1.10.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-278">jQuery UI 1.10.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-278">jQuery UI 1.10.0</span></span>](jquery-ui/cdnjqueryui1100.md "jQuery UI 1.10.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-279">jQuery UI 1.9.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-279">jQuery UI 1.9.2</span></span>](jquery-ui/cdnjqueryui192.md "jQuery UI 1.9.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-280">jQuery UI 1.9.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-280">jQuery UI 1.9.1</span></span>](jquery-ui/cdnjqueryui191.md "jQuery UI 1.9.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-281">jQuery UI 1.9.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-281">jQuery UI 1.9.0</span></span>](jquery-ui/cdnjqueryui190.md "jQuery UI 1.9.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-282">jQuery UI 1.8.24</span><span class="sxs-lookup"><span data-stu-id="7dcfa-282">jQuery UI 1.8.24</span></span>](jquery-ui/cdnjqueryui1824.md "jQuery UI 1.8.24 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-283">jQuery UI 1.8.23</span><span class="sxs-lookup"><span data-stu-id="7dcfa-283">jQuery UI 1.8.23</span></span>](jquery-ui/cdnjqueryui1823.md "jQuery UI 1.8.23 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-284">jQuery UI 1.8.22</span><span class="sxs-lookup"><span data-stu-id="7dcfa-284">jQuery UI 1.8.22</span></span>](jquery-ui/cdnjqueryui1822.md "jQuery UI 1.8.22 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-285">jQuery UI 1.8.21</span><span class="sxs-lookup"><span data-stu-id="7dcfa-285">jQuery UI 1.8.21</span></span>](jquery-ui/cdnjqueryui1821.md "jQuery UI 1.8.21 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-286">jQuery UI 1.8.20</span><span class="sxs-lookup"><span data-stu-id="7dcfa-286">jQuery UI 1.8.20</span></span>](jquery-ui/cdnjqueryui1820.md "jQuery UI 1.8.20 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-287">jQuery UI 1.8.19</span><span class="sxs-lookup"><span data-stu-id="7dcfa-287">jQuery UI 1.8.19</span></span>](jquery-ui/cdnjqueryui1819.md "jQuery UI 1.8.19 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-288">jQuery UI 1.8.18</span><span class="sxs-lookup"><span data-stu-id="7dcfa-288">jQuery UI 1.8.18</span></span>](jquery-ui/cdnjqueryui1818.md "jQuery UI 1.8.18 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-289">jQuery UI 1.8.17</span><span class="sxs-lookup"><span data-stu-id="7dcfa-289">jQuery UI 1.8.17</span></span>](jquery-ui/cdnjqueryui1817.md "jQuery UI 1.8.17 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-290">jQuery UI 1.8.16</span><span class="sxs-lookup"><span data-stu-id="7dcfa-290">jQuery UI 1.8.16</span></span>](jquery-ui/cdnjqueryui1816.md "jQuery UI 1.8.16 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-291">jQuery UI 1.8.15</span><span class="sxs-lookup"><span data-stu-id="7dcfa-291">jQuery UI 1.8.15</span></span>](jquery-ui/cdnjqueryui1815.md "jQuery UI 1.8.15 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-292">jQuery UI 1.8.14</span><span class="sxs-lookup"><span data-stu-id="7dcfa-292">jQuery UI 1.8.14</span></span>](jquery-ui/cdnjqueryui1814.md "jQuery UI 1.8.14 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-293">jQuery UI 1.8.13</span><span class="sxs-lookup"><span data-stu-id="7dcfa-293">jQuery UI 1.8.13</span></span>](jquery-ui/cdnjqueryui1813.md "jQuery UI 1.8.13 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-294">jQuery UI 1.8.12</span><span class="sxs-lookup"><span data-stu-id="7dcfa-294">jQuery UI 1.8.12</span></span>](jquery-ui/cdnjqueryui1812.md "jQuery UI 1.8.12 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-295">jQuery UI 1.8.11</span><span class="sxs-lookup"><span data-stu-id="7dcfa-295">jQuery UI 1.8.11</span></span>](jquery-ui/cdnjqueryui1811.md "jQuery UI 1.8.11 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-296">jQuery UI 1.8.10</span><span class="sxs-lookup"><span data-stu-id="7dcfa-296">jQuery UI 1.8.10</span></span>](jquery-ui/cdnjqueryui1910.md "jQuery UI 1.8.10 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-297">jQuery UI 1.8.9</span><span class="sxs-lookup"><span data-stu-id="7dcfa-297">jQuery UI 1.8.9</span></span>](jquery-ui/cdnjqueryui189.md "jQuery UI 1.8.9 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-298">jQuery UI 1.8.8</span><span class="sxs-lookup"><span data-stu-id="7dcfa-298">jQuery UI 1.8.8</span></span>](jquery-ui/cdnjqueryui188.md "jQuery UI 1.8.8 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-299">jQuery UI 1.8.7</span><span class="sxs-lookup"><span data-stu-id="7dcfa-299">jQuery UI 1.8.7</span></span>](jquery-ui/cdnjqueryui187.md "jQuery UI 1.8.7 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-300">jQuery UI 1.8.6</span><span class="sxs-lookup"><span data-stu-id="7dcfa-300">jQuery UI 1.8.6</span></span>](jquery-ui/cdnjqueryui186.md "jQuery UI 1.8.6 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-301">jQuery UI 1.8.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-301">jQuery UI 1.8.5</span></span>](jquery-ui/cdnjqueryui185.md "jQuery UI 1.8.5")

<a id="jQuery_Validation_Releases_on_the_CDN_3"></a>

### <a name="jquery-validation-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-302">jQuery-Validierung-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-302">jQuery Validation Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-303">Die folgenden Versionen der jQuery-Validierung-Bibliothek, die für dieses CDN gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-303">The following releases of the jQuery Validation library are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-304">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-304">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-305">jQuery Validate 1.19.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-305">jQuery Validate 1.19.0</span></span>](jquery-validate/cdnjqueryvalidate1190.md "jQuery-Validierung 1.19.0")
- [<span data-ttu-id="7dcfa-306">jQuery Validate 1.17.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-306">jQuery Validate 1.17.0</span></span>](jquery-validate/cdnjqueryvalidate1170.md "jQuery-Validierung 1.17.0")
- [<span data-ttu-id="7dcfa-307">jQuery Validate 1.16.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-307">jQuery Validate 1.16.0</span></span>](jquery-validate/cdnjqueryvalidate1160.md "jQuery-Validierung 1.16.0")
- [<span data-ttu-id="7dcfa-308">jQuery Validate 1.15.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-308">jQuery Validate 1.15.1</span></span>](jquery-validate/cdnjqueryvalidate1151.md "jQuery-Validierung 1.15.1")
- [<span data-ttu-id="7dcfa-309">jQuery Validate 1.15.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-309">jQuery Validate 1.15.0</span></span>](jquery-validate/cdnjqueryvalidate1150.md "jQuery-Validierung 1.15.0")
- [<span data-ttu-id="7dcfa-310">jQuery Validate 1.14.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-310">jQuery Validate 1.14.0</span></span>](jquery-validate/cdnjqueryvalidate1140.md "jQuery-Validierung 1.14.0")
- [<span data-ttu-id="7dcfa-311">jQuery Validate 1.13.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-311">jQuery Validate 1.13.1</span></span>](jquery-validate/cdnjqueryvalidate1131.md "jQuery-Validierung 1.13.1")
- [<span data-ttu-id="7dcfa-312">jQuery Validate 1.13.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-312">jQuery Validate 1.13.0</span></span>](jquery-validate/cdnjqueryvalidate1130.md "jQuery-Validierung 1.13.0")
- [<span data-ttu-id="7dcfa-313">jQuery Validate 1.12.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-313">jQuery Validate 1.12.0</span></span>](jquery-validate/cdnjqueryvalidate1120.md "jQuery-Validierung 1.12.0")
- [<span data-ttu-id="7dcfa-314">jQuery Validate 1.11.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-314">jQuery Validate 1.11.1</span></span>](jquery-validate/cdnjqueryvalidate1111.md "jQuery-Validierung 1.11.1")
- [<span data-ttu-id="7dcfa-315">jQuery Validate 1.11.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-315">jQuery Validate 1.11.0</span></span>](jquery-validate/cdnjqueryvalidate111.md "jQuery-Validierung 1.11.0")
- [<span data-ttu-id="7dcfa-316">jQuery Validate 1.10.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-316">jQuery Validate 1.10.0</span></span>](jquery-validate/cdnjqueryvalidate110.md "jQuery-Validierung 1.10.0")
- [<span data-ttu-id="7dcfa-317">jQuery Validate 1.9</span><span class="sxs-lookup"><span data-stu-id="7dcfa-317">jQuery Validate 1.9</span></span>](jquery-validate/cdnjqueryvalidate19.md "jquery.validate Version 1.9")
- [<span data-ttu-id="7dcfa-318">jQuery Validate 1.8.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-318">jQuery Validate 1.8.1</span></span>](jquery-validate/cdnjqueryvalidate181.md "jquery.validate Version 1.8.1")
- [<span data-ttu-id="7dcfa-319">jQuery Validate 1.8</span><span class="sxs-lookup"><span data-stu-id="7dcfa-319">jQuery Validate 1.8</span></span>](jquery-validate/cdnjqueryvalidate18.md "jquery.validate Version 1.8")
- [<span data-ttu-id="7dcfa-320">jQuery Validate 1.7</span><span class="sxs-lookup"><span data-stu-id="7dcfa-320">jQuery Validate 1.7</span></span>](jquery-validate/cdnjqueryvalidate17.md "jquery.validate Version 1.7")
- [<span data-ttu-id="7dcfa-321">jQuery Validate 1.6</span><span class="sxs-lookup"><span data-stu-id="7dcfa-321">jQuery Validate 1.6</span></span>](jquery-validate/cdnjqueryvalidate16.md "jQuery Validate 1.6")
- [<span data-ttu-id="7dcfa-322">jQuery Validate 1.5.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-322">jQuery Validate 1.5.5</span></span>](jquery-validate/cdnjqueryvalidate155.md "jQuery Validate 1.5.5")

<a id="jQuery_Mobile_Releases_on_the_CDN_4"></a>

### <a name="jquery-mobile-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-323">jQuery Mobile-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-323">jQuery Mobile Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-324">Die folgenden Versionen der mobilen jQuery-Bibliothek, die auf dieses CDN gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-324">The following releases of the jQuery Mobile library are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-325">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-325">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-326">jQuery Mobile 1.4.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-326">jQuery Mobile 1.4.5</span></span>](jquery-mobile/cdnjquerymobile145.md "jQuery Mobile 1.4.5 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-327">jQuery Mobile 1.4.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-327">jQuery Mobile 1.4.2</span></span>](jquery-mobile/cdnjquerymobile142.md "jQuery Mobile 1.4.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-328">jQuery Mobile 1.4.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-328">jQuery Mobile 1.4.1</span></span>](jquery-mobile/cdnjquerymobile141.md "jQuery Mobile 1.4.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-329">jQuery Mobile 1.4.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-329">jQuery Mobile 1.4.0</span></span>](jquery-mobile/cdnjquerymobile140.md "jQuery Mobile 1.4.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-330">jQuery Mobile 1.3.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-330">jQuery Mobile 1.3.2</span></span>](jquery-mobile/cdnjquerymobile132.md "jQuery Mobile 1.3.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-331">jQuery Mobile 1.3.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-331">jQuery Mobile 1.3.1</span></span>](jquery-mobile/cdnjquerymobile131.md "jQuery Mobile 1.3.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-332">jQuery Mobile 1.3.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-332">jQuery Mobile 1.3.0</span></span>](jquery-mobile/cdnjquerymobile130.md "jQuery Mobile 1.3.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-333">jQuery Mobile 1.2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-333">jQuery Mobile 1.2.0</span></span>](jquery-mobile/cdnjquerymobile120.md "jQuery Mobile 1.2.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-334">jQuery Mobile 1.1.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-334">jQuery Mobile 1.1.2</span></span>](jquery-mobile/cdnjquerymobile112.md "jQuery Mobile 1.1.2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-335">jQuery Mobile 1.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-335">jQuery Mobile 1.1.1</span></span>](jquery-mobile/cdnjquerymobile111.md "jQuery Mobile 1.1.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-336">jQuery Mobile 1.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-336">jQuery Mobile 1.1.0</span></span>](jquery-mobile/cdnjquerymobile110.md "jQuery Mobile 1.1.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-337">jQuery Mobile 1.1.0 RC 2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-337">jQuery Mobile 1.1.0 RC 2</span></span>](jquery-mobile/cdnjquerymobile110rc2.md "jQuery Mobile 1.1.0 RC2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-338">jQuery Mobile 1.0.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-338">jQuery Mobile 1.0.1</span></span>](jquery-mobile/cdnjquerymobile101.md "jQuery Mobile 1.0.1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-339">jQuery Mobile 1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-339">jQuery Mobile 1.0</span></span>](jquery-mobile/cdnjquerymobile10.md "jQuery Mobile 1.0 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-340">jQuery Mobile 1.0 RC2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-340">jQuery Mobile 1.0 RC 2</span></span>](jquery-mobile/cdnjquerymobile10rc2.md "jQuery Mobile 1.0 RC2 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-341">jQuery Mobile 1.0 RC 1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-341">jQuery Mobile 1.0 RC 1</span></span>](jquery-mobile/cdnjquerymobile10rc1.md "jQuery Mobile 1.0 RC1 für das Microsoft Ajax CDN")
- [<span data-ttu-id="7dcfa-342">jQuery Mobile 1.0 Beta 3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-342">jQuery Mobile 1.0 beta 3</span></span>](jquery-mobile/cdnjquerymobile10b3.md "jQuery Mobile 1.0 Beta 3 für das Microsoft Ajax CDN")

<a id="jQuery_Templates_Releases_on_the_CDN_5"></a>

### <a name="jquery-templates-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-343">jQuery-Vorlagen-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-343">jQuery Templates Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-344">Die folgenden Versionen der jQuery-Vorlagen-Plug-Ins werden auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-344">The following releases of the jQuery Templates plugin are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-345">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-345">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-346">jQuery-Vorlagen Beta 1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-346">jQuery Templates Beta 1</span></span>](jquery-templates/cdnjquerytemplatesbeta1.md "jQuery-Vorlagen Beta 1")

<a id="jQuery_Cycle_Releases_on_the_CDN_6"></a>

### <a name="jquery-cycle-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-347">jQuery-Zyklus-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-347">jQuery Cycle Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-348">Die folgenden Versionen der jQuery-Zyklus-Plug-Ins werden auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-348">The following releases of the jQuery Cycle plugin are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-349">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-349">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-350">jQuery-Zyklus 2,99</span><span class="sxs-lookup"><span data-stu-id="7dcfa-350">jQuery Cycle 2.99</span></span>](jquery-cycle/cdnjquerycycle299.md "jQuery Zyklus 2,99")
- [<span data-ttu-id="7dcfa-351">jQuery-Zyklus 2.94</span><span class="sxs-lookup"><span data-stu-id="7dcfa-351">jQuery Cycle 2.94</span></span>](jquery-cycle/cdnjquerycycle294.md "jQuery Zyklus 2.94")
- [<span data-ttu-id="7dcfa-352">jQuery-Zyklus 2,88</span><span class="sxs-lookup"><span data-stu-id="7dcfa-352">jQuery Cycle 2.88</span></span>](jquery-cycle/cdnjquerycycle288.md "jQuery Zyklus 2,88")

<a id="jQuery_DataTables_Releases_on_the_CDN_7"></a>

### <a name="jquery-datatables-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-353">jQuery-DataTables-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-353">jQuery DataTables Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-354">Die folgenden Versionen der jQuery-DataTables-Plug-Ins werden auf dieses CDN gehostet.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-354">The following releases of the jQuery DataTables plugin are hosted on this CDN.</span></span> <span data-ttu-id="7dcfa-355">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-355">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-356">jQuery-DataTables 1.10.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-356">jQuery DataTables 1.10.5</span></span>](jquery-datatables/cdnjquerydatatables105.md "jQuery-DataTables 1.10.5")
- [<span data-ttu-id="7dcfa-357">jQuery-DataTables 1.10.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-357">jQuery DataTables 1.10.4</span></span>](jquery-datatables/cdnjquerydatatables104.md "jQuery-DataTables 1.10.4")
- [<span data-ttu-id="7dcfa-358">jQuery-DataTables 1.9.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-358">jQuery DataTables 1.9.4</span></span>](jquery-datatables/cdnjquerydatatables194.md "jQuery-DataTables 1.9.4")
- [<span data-ttu-id="7dcfa-359">jQuery-DataTables 1.9.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-359">jQuery DataTables 1.9.3</span></span>](jquery-datatables/cdnjquerydatatables193.md "jQuery-DataTables 1.9.3")
- [<span data-ttu-id="7dcfa-360">jQuery-DataTables 1.9.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-360">jQuery DataTables 1.9.2</span></span>](jquery-datatables/cdnjquerydatatables192.md "jQuery-DataTables 1.9.2")
- [<span data-ttu-id="7dcfa-361">jQuery-DataTables 1.9.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-361">jQuery DataTables 1.9.1</span></span>](jquery-datatables/cdnjquerydatatables191.md "jQuery-DataTables 1.9.1")
- [<span data-ttu-id="7dcfa-362">jQuery-DataTables 1.9.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-362">jQuery DataTables 1.9.0</span></span>](jquery-datatables/cdnjquerydatatables190.md "jQuery-DataTables 1.9.0")
- [<span data-ttu-id="7dcfa-363">jQuery-DataTables 1.8.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-363">jQuery DataTables 1.8.2</span></span>](jquery-datatables/cdnjquerydatatables182.md "jQuery-DataTables 1.8.2")

<a id="Modernizr_Releases_on_the_CDN_8"></a>

### <a name="modernizr-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-364">Modernizr-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-364">Modernizr Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-365">Die folgenden Versionen von [Modernizr](http://www.modernizr.com "Modernizr") für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-365">The following releases of [Modernizr](http://www.modernizr.com "Modernizr") are hosted on the CDN:</span></span>

- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-3.5.0.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-2.8.3.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-2.7.2.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-2.7.1.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-2.6.2.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-1.7-development-only.js
- https://ajax.aspnetcdn.com/ajax/modernizr/modernizr-2.0.6-development-only.js

<a id="JSHint_Releases_on_the_CDN_10"></a>

### <a name="jshint-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-366">JSHint-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-366">JSHint Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-367">Die folgenden Versionen von [JSHint](http://www.jshint.com "JSHint") für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-367">The following releases of [JSHint](http://www.jshint.com "JSHint") are hosted on the CDN:</span></span>

- https://ajax.aspnetcdn.com/ajax/jshint/r07/jshint.js

<a id="Knockout_Releases_on_the_CDN_11"></a>

### <a name="knockout-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-368">Knockout-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-368">Knockout Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-369">Die folgenden Versionen von [Knockout](http://www.knockoutjs.com "Knockout") für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-369">The following releases of [Knockout](http://www.knockoutjs.com "Knockout") are hosted on the CDN:</span></span>

- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.2.1.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.2.1.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.2.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.2.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-2.1.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.0.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.0.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.1.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.1.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.2.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.2.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.0.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.0.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.1.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.1.debug.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.2.js
- https://ajax.aspnetcdn.com/ajax/knockout/knockout-3.4.2.debug.js

<a id="Globalize_Releases_on_the_CDN_12"></a>

### <a name="globalize-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-370">Globalisieren von Releases auf dem CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-370">Globalize Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-371">Die folgenden Versionen von [Globalize](https://github.com/jquery/globalize "Globalize") für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-371">The following releases of [Globalize](https://github.com/jquery/globalize "Globalize") are hosted on the CDN:</span></span>

#### <a name="globalize-version-100"></a><span data-ttu-id="7dcfa-372">Globalisieren von Version 1.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-372">Globalize version 1.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/node-main.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/currency.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/date.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/message.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/number.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/plural.js
- https://ajax.aspnetcdn.com/ajax/globalize/1.0.0/globalize/relative-time.js

#### <a name="globalize-version-011"></a><span data-ttu-id="7dcfa-373">Globalisieren von Version 0.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-373">Globalize version 0.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/globalize/0.1.1/globalize.min.js
- https://ajax.aspnetcdn.com/ajax/globalize/0.1.1/globalize.js
- https://ajax.aspnetcdn.com/ajax/globalize/0.1.1/cultures/globalize.cultures.js

    - <span data-ttu-id="7dcfa-374">alle Kulturen</span><span class="sxs-lookup"><span data-stu-id="7dcfa-374">all cultures</span></span>
- https://ajax.aspnetcdn.com/ajax/globalize/0.1.1/cultures/globalize.culture.{culture-code}.js

    - <span data-ttu-id="7dcfa-375">Ersetzen Sie "{Kulturcode}" durch den Code für die gewünschte Sprache, z. B. globalize.culture.en-GB.js== Microsoft Dateien für das CDN == diese Bibliotheken wurden von Microsoft hochgeladen.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-375">Replace "{culture-code}" with the desired culture code, e.g. globalize.culture.en-GB.js== Microsoft Files on the CDN ==These libraries were uploaded by Microsoft.</span></span>

<a id="Respond_Releases_on_the_CDN_13"></a>

### <a name="respond-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-376">Reagieren Sie Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-376">Respond Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-377">Die folgenden Versionen von [https://github.com/scottjehl/Respond](https://github.com/scottjehl/Respond "https://github.com/scottjehl/Respond") reagieren auf das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-377">The following releases of [https://github.com/scottjehl/Respond](https://github.com/scottjehl/Respond "https://github.com/scottjehl/Respond") Respond are hosted on the CDN:</span></span>

#### <a name="respond-version-142"></a><span data-ttu-id="7dcfa-378">Version 1.4.2 reagieren</span><span class="sxs-lookup"><span data-stu-id="7dcfa-378">Respond version 1.4.2</span></span>

- https://ajax.aspnetcdn.com/ajax/respond/1.4.2/respond.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.2/respond.min.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.2/respond.matchmedia.addListener.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.2/respond.matchmedia.addListener.min.js

#### <a name="respond-version-141"></a><span data-ttu-id="7dcfa-379">Version 1.4.1 reagieren</span><span class="sxs-lookup"><span data-stu-id="7dcfa-379">Respond version 1.4.1</span></span>

- https://ajax.aspnetcdn.com/ajax/respond/1.4.1/respond.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.1/respond.min.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.1/respond.matchmedia.addListener.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.1/respond.matchmedia.addListener.min.js

#### <a name="respond-version-140"></a><span data-ttu-id="7dcfa-380">Reagieren Sie Version 1.4.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-380">Respond version 1.4.0</span></span>

- https://ajax.aspnetcdn.com/ajax/respond/1.4.0/respond.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.0/respond.min.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.0/respond.matchmedia.addListener.js
- https://ajax.aspnetcdn.com/ajax/respond/1.4.0/respond.matchmedia.addListener.min.js

#### <a name="respond-version-130"></a><span data-ttu-id="7dcfa-381">Version 1.3.0 reagieren</span><span class="sxs-lookup"><span data-stu-id="7dcfa-381">Respond version 1.3.0</span></span>

- https://ajax.aspnetcdn.com/ajax/respond/1.3.0/respond.js

#### <a name="respond-version-120"></a><span data-ttu-id="7dcfa-382">Version 1.2.0 reagieren</span><span class="sxs-lookup"><span data-stu-id="7dcfa-382">Respond version 1.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/respond/1.2.0/respond.js

<a id="Bootstrap_Releases_on_the_CDN_14"></a>

### <a name="bootstrap-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-383">Bootstrap-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-383">Bootstrap Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-384">Die folgenden Versionen von [getbootstrap.com](http://getbootstrap.com "getbootstrap.com") Bootstrap für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-384">The following releases of [getbootstrap.com](http://getbootstrap.com "getbootstrap.com") bootstrap are hosted on the CDN:</span></span>

#### <a name="bootstrap-version-411"></a><span data-ttu-id="7dcfa-385">Bootstrap Version 4.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-385">Bootstrap version 4.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/bootstrap.bundle.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-grid.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-grid.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-grid.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-reboot.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-reboot.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.1.1/css/bootstrap-reboot.css.map

#### <a name="bootstrap-version-400"></a><span data-ttu-id="7dcfa-386">Bootstrap-Version 4.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-386">Bootstrap version 4.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/bootstrap.bundle.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-grid.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-grid.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-grid.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-reboot.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-reboot.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/4.0.0/css/bootstrap-reboot.css.map

#### <a name="bootstrap-version-337"></a><span data-ttu-id="7dcfa-387">Bootstrap Version 3.3.7</span><span class="sxs-lookup"><span data-stu-id="7dcfa-387">Bootstrap version 3.3.7</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/fonts/glyphicons-halflings-regular.woff
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/fonts/glyphicons-halflings-regular.woff2

#### <a name="bootstrap-version-336"></a><span data-ttu-id="7dcfa-388">Bootstrap Version 3.3.6</span><span class="sxs-lookup"><span data-stu-id="7dcfa-388">Bootstrap version 3.3.6</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/fonts/glyphicons-halflings-regular.woff
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/fonts/glyphicons-halflings-regular.woff2

#### <a name="bootstrap-version-335"></a><span data-ttu-id="7dcfa-389">Bootstrap Version 3.3.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-389">Bootstrap version 3.3.5</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/fonts/glyphicons-halflings-regular.woff
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.5/fonts/glyphicons-halflings-regular.woff2

#### <a name="bootstrap-version-334"></a><span data-ttu-id="7dcfa-390">Bootstrap Version 3.3.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-390">Bootstrap version 3.3.4</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/fonts/glyphicons-halflings-regular.woff
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.4/fonts/glyphicons-halflings-regular.woff2

#### <a name="bootstrap-version-332"></a><span data-ttu-id="7dcfa-391">Bootstrap Version 3.3.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-391">Bootstrap version 3.3.2</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/fonts/glyphicons-halflings-regular.woff
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/fonts/glyphicons-halflings-regular.woff2

#### <a name="bootstrap-version-331"></a><span data-ttu-id="7dcfa-392">Bootstrap-Version 3.3.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-392">Bootstrap version 3.3.1</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.1/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-330"></a><span data-ttu-id="7dcfa-393">Bootstrap Version 3.3.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-393">Bootstrap version 3.3.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.0/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-320"></a><span data-ttu-id="7dcfa-394">Bootstrap Version 3.2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-394">Bootstrap version 3.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-311"></a><span data-ttu-id="7dcfa-395">Bootstrap Version 3.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-395">Bootstrap version 3.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.1/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-310"></a><span data-ttu-id="7dcfa-396">Bootstrap Version 3.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-396">Bootstrap version 3.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap-theme.css.map
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.1.0/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-303"></a><span data-ttu-id="7dcfa-397">Bootstrap Version 3.0.3.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-397">Bootstrap version 3.0.3</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.3/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-302"></a><span data-ttu-id="7dcfa-398">Bootstrap Version 3.0.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-398">Bootstrap version 3.0.2</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.2/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-301"></a><span data-ttu-id="7dcfa-399">Bootstrap-Version 3.0.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-399">Bootstrap version 3.0.1</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.1/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-300"></a><span data-ttu-id="7dcfa-400">Bootstrap Version 3.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-400">Bootstrap version 3.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/css/bootstrap-theme.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/css/bootstrap-theme.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/fonts/glyphicons-halflings-regular.eot
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/fonts/glyphicons-halflings-regular.svg
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/fonts/glyphicons-halflings-regular.ttf
- https://ajax.aspnetcdn.com/ajax/bootstrap/3.0.0/fonts/glyphicons-halflings-regular.woff

#### <a name="bootstrap-version-232"></a><span data-ttu-id="7dcfa-401">Bootstrap-Version 2.3.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-401">Bootstrap version 2.3.2</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/css/bootstrap-responsive.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/css/bootstrap-responsive.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/img/glyphicons-halflings.png
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.2/img/glyphicons-halflings-white.png

#### <a name="bootstrap-version-231"></a><span data-ttu-id="7dcfa-402">Bootstrap-Version 2.3.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-402">Bootstrap version 2.3.1</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/bootstrap.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/bootstrap.min.js
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/css/bootstrap.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/css/bootstrap.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/css/bootstrap-responsive.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/css/bootstrap-responsive.min.css
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/img/glyphicons-halflings.png
- https://ajax.aspnetcdn.com/ajax/bootstrap/2.3.1/img/glyphicons-halflings-white.png

<a id="BootstrapTouchCarousel_Releases_on_the_CDN_18"></a>

### <a name="bootstrap-touchcarousel-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-403">Bootstrap TouchCarousel-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-403">Bootstrap TouchCarousel Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-404">Die folgenden Versionen von [https://github.com/ixisio/bootstrap-touch-carousel](https://github.com/ixisio/bootstrap-touch-carousel "https://github.com/ixisio/bootstrap-touch-carousel") TouchCarousel der Bootstrap-Versionen für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-404">The following releases of [https://github.com/ixisio/bootstrap-touch-carousel](https://github.com/ixisio/bootstrap-touch-carousel "https://github.com/ixisio/bootstrap-touch-carousel") Bootstrap TouchCarousel releases are hosted on the CDN:</span></span>

#### <a name="bootstrap-touchcarousel-version-080"></a><span data-ttu-id="7dcfa-405">Bootstrap TouchCarousel Version 0.8.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-405">Bootstrap TouchCarousel version 0.8.0</span></span>

- https://ajax.aspnetcdn.com/ajax/bootstrap-touch-carousel/0.8.0/css/bootstrap-touch-carousel.css
- https://ajax.aspnetcdn.com/ajax/bootstrap-touch-carousel/0.8.0/js/bootstrap-touch-carousel.js

<a id="Hammerjs_Releases_on_the_CDN_19"></a>

### <a name="hammerjs-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-406">Hammer.js-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-406">Hammer.js Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-407">Die folgenden Versionen von [http://hammerjs.github.io/](http://hammerjs.github.io/ "http://hammerjs.github.io/") Hammer.js-Versionen für das CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-407">The following releases of [http://hammerjs.github.io/](http://hammerjs.github.io/ "http://hammerjs.github.io/") Hammer.js releases are hosted on the CDN:</span></span>

#### <a name="hammerjs-version-204"></a><span data-ttu-id="7dcfa-408">Hammer.js Version 2.0.4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-408">Hammer.js version 2.0.4</span></span>

- https://ajax.aspnetcdn.com/ajax/hammer.js/2.0.4/hammer.js
- https://ajax.aspnetcdn.com/ajax/hammer.js/2.0.4/hammer.min.js
- https://ajax.aspnetcdn.com/ajax/hammer.js/2.0.4/hammer.min.map

<a id="ASPNET_Web_Forms_and_Ajax_Releases_on_the_CDN_15"></a>

### <a name="aspnet-web-forms-and-ajax-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-409">ASP.NET Web Forms und Ajax-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-409">ASP.NET Web Forms and Ajax Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-410">Die folgenden Versionen von ASP.NET Ajax-Bibliothek, die für das CDN gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-410">The following releases of the ASP.NET Ajax Library are hosted on the CDN.</span></span> <span data-ttu-id="7dcfa-411">Klicken Sie auf jeden Link, um die tatsächliche Liste von Dateien.</span><span class="sxs-lookup"><span data-stu-id="7dcfa-411">Click each link to see the actual list of files.</span></span>

- [<span data-ttu-id="7dcfa-412">ASP.NET Web Forms und Ajax-Version 4.5.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-412">ASP.NET Web Forms and Ajax version 4.5.2</span></span>](cdnajax452.md "ASP.NET Web Forms und Ajax 4.5.2")
- [<span data-ttu-id="7dcfa-413">ASP.NET Web Forms und Ajax-Version 4</span><span class="sxs-lookup"><span data-stu-id="7dcfa-413">ASP.NET Web Forms and Ajax version 4</span></span>](cdnajax4.md "ASP.NET Web Forms und Ajax 4")
- [<span data-ttu-id="7dcfa-414">ASP.NET Ajax Version 3.5</span><span class="sxs-lookup"><span data-stu-id="7dcfa-414">ASP.NET Ajax version 3.5</span></span>](cdnajax35.md "ASP.NET Ajax 3.5")

<a id="ASPNET_MVC_Releases_on_the_CDN_16"></a>

### <a name="aspnet-mvc-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-415">ASP.NET MVC-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-415">ASP.NET MVC Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-416">Die folgenden ASP.NET MVC-JavaScript-Dateien, die auf dieses CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-416">The following ASP.NET MVC JavaScript files are hosted on this CDN:</span></span>

#### <a name="aspnet-mvc-523"></a><span data-ttu-id="7dcfa-417">ASP.NET MVC 5.2.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-417">ASP.NET MVC 5.2.3</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/5.2.3/jquery.validate.unobtrusive.js
- https://ajax.aspnetcdn.com/ajax/mvc/5.2.3/jquery.validate.unobtrusive.min.js

#### <a name="aspnet-mvc-51"></a><span data-ttu-id="7dcfa-418">ASP.NET MVC 5.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-418">ASP.NET MVC 5.1</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/5.1/jquery.validate.unobtrusive.js
- https://ajax.aspnetcdn.com/ajax/mvc/5.1/jquery.validate.unobtrusive.min.js

#### <a name="aspnet-mvc-50"></a><span data-ttu-id="7dcfa-419">ASP.NET MVC 5.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-419">ASP.NET MVC 5.0</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/5.0/jquery.validate.unobtrusive.js
- https://ajax.aspnetcdn.com/ajax/mvc/5.0/jquery.validate.unobtrusive.min.js

#### <a name="aspnet-mvc-40"></a><span data-ttu-id="7dcfa-420">ASP.NET MVC 4.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-420">ASP.NET MVC 4.0</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/4.0/jquery.validate.unobtrusive.js
- https://ajax.aspnetcdn.com/ajax/mvc/4.0/jquery.validate.unobtrusive.min.js

#### <a name="aspnet-mvc-30"></a><span data-ttu-id="7dcfa-421">ASP.NET MVC 3.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-421">ASP.NET MVC 3.0</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/3.0/jquery.unobtrusive-ajax.js
- https://ajax.aspnetcdn.com/ajax/mvc/3.0/jquery.unobtrusive-ajax.min.js
- https://ajax.aspnetcdn.com/ajax/jquery.unobtrusive-ajax/3.2.5/jquery.unobtrusive-ajax.js
- https://ajax.aspnetcdn.com/ajax/jquery.unobtrusive-ajax/3.2.5/jquery.unobtrusive-ajax.min.js 
- https://ajax.aspnetcdn.com/ajax/mvc/3.0/jquery.validate.unobtrusive.js
- https://ajax.aspnetcdn.com/ajax/mvc/3.0/jquery.validate.unobtrusive.min.js
- https://ajax.aspnetcdn.com/ajax/jquery.validation.unobtrusive/3.2.10/jquery.validate.unobtrusive.js 
- https://ajax.aspnetcdn.com/ajax/jquery.validation.unobtrusive/3.2.10/jquery.validate.unobtrusive.min.js
- https://ajax.aspnetcdn.com/ajax/mvc/3.0/MicrosoftMvcAjax.js
- https://ajax.aspnetcdn.com/ajax/mvc/3.0/MicrosoftMvcAjax.debug.js

#### <a name="aspnet-mvc-20"></a><span data-ttu-id="7dcfa-422">ASP.NET MVC 2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-422">ASP.NET MVC 2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/2.0/MicrosoftMvcAjax.js
- https://ajax.aspnetcdn.com/ajax/mvc/2.0/MicrosoftMvcAjax.debug.js

#### <a name="aspnet-mvc-10"></a><span data-ttu-id="7dcfa-423">ASP.NET MVC 1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-423">ASP.NET MVC 1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/mvc/1.0/MicrosoftMvcAjax.js
- https://ajax.aspnetcdn.com/ajax/mvc/1.0/MicrosoftMvcAjax.debug.js

<a id="ASPNET_SignalR_Releases_on_the_CDN_17"></a>

### <a name="aspnet-signalr-releases-on-the-cdn"></a><span data-ttu-id="7dcfa-424">ASP.NET SignalR-Versionen für das CDN</span><span class="sxs-lookup"><span data-stu-id="7dcfa-424">ASP.NET SignalR Releases on the CDN</span></span>

<span data-ttu-id="7dcfa-425">Die folgenden ASP.NET SignalR JavaScript-Dateien, die auf dieses CDN gehostet werden:</span><span class="sxs-lookup"><span data-stu-id="7dcfa-425">The following ASP.NET SignalR JavaScript files are hosted on this CDN:</span></span>

#### <a name="aspnet-signalr-222"></a><span data-ttu-id="7dcfa-426">ASP.NET SignalR 2.2.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-426">ASP.NET SignalR 2.2.2</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.2.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.2.js

#### <a name="aspnet-signalr-221"></a><span data-ttu-id="7dcfa-427">ASP.NET SignalR 2.2.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-427">ASP.NET SignalR 2.2.1</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.1.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.1.js

#### <a name="aspnet-signalr-220"></a><span data-ttu-id="7dcfa-428">ASP.NET SignalR 2.2.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-428">ASP.NET SignalR 2.2.0</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.0.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.2.0.js

#### <a name="aspnet-signalr-210"></a><span data-ttu-id="7dcfa-429">ASP.NET SignalR 2.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-429">ASP.NET SignalR 2.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.1.0.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.1.0.js

#### <a name="aspnet-signalr-203"></a><span data-ttu-id="7dcfa-430">ASP.NET SignalR 2.0.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-430">ASP.NET SignalR 2.0.3</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.3.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.3.js

#### <a name="aspnet-signalr-202"></a><span data-ttu-id="7dcfa-431">ASP.NET SignalR 2.0.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-431">ASP.NET SignalR 2.0.2</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.2.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.2.js

#### <a name="aspnet-signalr-201"></a><span data-ttu-id="7dcfa-432">ASP.NET SignalR 2.0.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-432">ASP.NET SignalR 2.0.1</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.1.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.1.js

#### <a name="aspnet-signalr-200"></a><span data-ttu-id="7dcfa-433">ASP.NET SignalR 2.0.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-433">ASP.NET SignalR 2.0.0</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.0.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-2.0.0.js

#### <a name="aspnet-signalr-113"></a><span data-ttu-id="7dcfa-434">ASP.NET SignalR 1.1.3</span><span class="sxs-lookup"><span data-stu-id="7dcfa-434">ASP.NET SignalR 1.1.3</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.3.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.3.js

#### <a name="aspnet-signalr-112"></a><span data-ttu-id="7dcfa-435">ASP.NET SignalR 1.1.2</span><span class="sxs-lookup"><span data-stu-id="7dcfa-435">ASP.NET SignalR 1.1.2</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.2.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.2.js

#### <a name="aspnet-signalr-111"></a><span data-ttu-id="7dcfa-436">ASP.NET SignalR 1.1.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-436">ASP.NET SignalR 1.1.1</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.1.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.1.js

#### <a name="aspnet-signalr-110"></a><span data-ttu-id="7dcfa-437">ASP.NET SignalR 1.1.0</span><span class="sxs-lookup"><span data-stu-id="7dcfa-437">ASP.NET SignalR 1.1.0</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.0.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.1.0.js

#### <a name="aspnet-signalr-101"></a><span data-ttu-id="7dcfa-438">ASP.NET SignalR 1.0.1</span><span class="sxs-lookup"><span data-stu-id="7dcfa-438">ASP.NET SignalR 1.0.1</span></span>

- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.0.1.min.js
- https://ajax.aspnetcdn.com/ajax/signalr/jquery.signalr-1.0.1.js

<span data-ttu-id="7dcfa-439">Weitere Informationen zu den Nutzungsbedingungen für das CDN, finden Sie unter [Microsoft Ajax CDN-Nutzungsbedingungen](https://www.asp.net/terms-of-use "Microsoft Ajax CDN-Nutzungsbedingungen").</span><span class="sxs-lookup"><span data-stu-id="7dcfa-439">For information about the terms of use for the CDN, see [Microsoft Ajax CDN Terms of Use](https://www.asp.net/terms-of-use "Microsoft Ajax CDN Terms of Use").</span></span>
