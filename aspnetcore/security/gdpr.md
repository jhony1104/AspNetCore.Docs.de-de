---
title: Unterstützung für Datenschutz-Grundverordnung (dsgvo) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie in einer ASP.net Core Web-App auf die dsgvo-Erweiterungs Punkte zugreifen.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 2e21f54ebfcb55be2b97da217b92a39843b5d702
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003212"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9a4a8-103">Unterstützung der EU-Datenschutz-Grundverordnung (dsgvo) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9a4a8-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9a4a8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a4a8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9a4a8-105">ASP.net Core bietet APIs und Vorlagen, mit denen einige der Anforderungen der [EU-Datenschutz-Grundverordnung (](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) dsgvo) erfüllt werden können:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9a4a8-106">Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und Cookie-Nutzungsrichtlinie ersetzen können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a4a8-107">Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9a4a8-108">So aktivieren Sie das standardmäßige Cookie-Zustimmungs Feature wie das in den ASP.net Core 2,2-Vorlagen in einer von ASP.net Core 3,0-Vorlagen generierten App:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9a4a8-109">Fügen `using Microsoft.AspNetCore.Http` Sie der Liste der using-Direktiven hinzu.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9a4a8-110">Fügen Sie [cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) zu `Startup.ConfigureServices` und [usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) hinzu `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9a4a8-111">Fügen Sie die Cookie-Zustimmung partiell der Datei *_Layout. cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9a4a8-112">Fügen Sie dem Projekt die \* \_Datei cookiegenehmipartial. cshtml\* hinzu:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="9a4a8-113">Wählen Sie die ASP.net Core 2,2-Version dieses Artikels aus, um Informationen über die Cookie-Zustimmungs Funktion zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9a4a8-114">Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und Cookie-Nutzungsrichtlinie ersetzen können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a4a8-115">Mit der Funktion "Cookie-Zustimmung" können Sie Ihre Benutzer für die Speicherung persönlicher Informationen auffordern (und nachverfolgen).</span><span class="sxs-lookup"><span data-stu-id="9a4a8-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9a4a8-116">Wenn ein Benutzer der Datensammlung nicht zugestimmt hat und für die APP [checkgenehmigbar](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) auf `true`festgelegt ist, werden nicht erforderliche Cookies nicht an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="9a4a8-117">Cookies können als unverzichtbar gekennzeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="9a4a8-118">Wichtige Cookies werden auch dann an den Browser gesendet, wenn der Benutzer nicht zugestimmt hat und die Nachverfolgung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9a4a8-119">[TempData-und Sitzungs Cookies](#tempdata) sind nicht funktionsfähig, wenn die Nachverfolgung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9a4a8-120">Die Seite [Identitätsverwaltung](#pd) enthält einen Link zum herunterladen und Löschen von Benutzerdaten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9a4a8-121">Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) können Sie die meisten dsgvo-Erweiterungs Punkte und APIs testen, die den ASP.net Core 2,1-Vorlagen hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9a4a8-122">Weitere Informationen finden Sie [in der](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) Infodatei.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9a4a8-123">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a4a8-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9a4a8-124">ASP.net Core dsgvo-Unterstützung in Vorlagen generiertem Code</span><span class="sxs-lookup"><span data-stu-id="9a4a8-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="9a4a8-125">Razor Pages-und MVC-Projekte, die mit den Projektvorlagen erstellt werden, enthalten die folgende dsgvo-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="9a4a8-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9a4a8-126">[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) und [usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) werden in der `Startup` -Klasse festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9a4a8-127">Die \* \_Teilansicht cookiegenehmipartial. cshtml\* . [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="9a4a8-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9a4a8-128">Eine **Accept** -Schaltfläche ist in dieser Datei enthalten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9a4a8-129">Wenn der Benutzer auf die Schaltfläche **annehmen** klickt, wird die Zustimmung zum Speichern von Cookies bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="9a4a8-130">Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9a4a8-131">Die \* \_cookiegenehmipartial. cshtml\* -Datei generiert einen Link zur Datenschutzseite.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9a4a8-132">Für apps, die mit einzelnen Benutzerkonten erstellt wurden, stellt die Seite verwalten Links zum herunterladen und Löschen [persönlicher Benutzerdaten](#pd)bereit.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="9a4a8-133">Cookiepolicyoptions und usecookiepolicy</span><span class="sxs-lookup"><span data-stu-id="9a4a8-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="9a4a8-134">[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) werden in `Startup.ConfigureServices`initialisiert:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9a4a8-135">[Usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) wird in `Startup.Configure`aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9a4a8-136">\_Cookieeinvernehmliche partielle. cshtml-Teilansicht</span><span class="sxs-lookup"><span data-stu-id="9a4a8-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9a4a8-137">Die Teilansicht \* \_cookiegenehmipartial. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="9a4a8-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="9a4a8-138">Diese partielle:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-138">This partial:</span></span>

* <span data-ttu-id="9a4a8-139">Ruft den Status der Nachverfolgung für den Benutzer ab.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9a4a8-140">Wenn die APP so konfiguriert ist, dass Sie Zustimmung erfordert, muss der Benutzer zustimmen, bevor Cookies nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="9a4a8-141">Wenn die Zustimmung erforderlich ist, wird der Bereich für die Cookie-Zustimmung am oberen Rand der Navigationsleiste, die von der \* \_Datei "Layout. cshtml\* " erstellt wurde, korrigiert.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9a4a8-142">Stellt ein HTML `<p>` -Element bereit, um Ihre Datenschutz-und Richtlinien zur Verwendung von Cookies</span><span class="sxs-lookup"><span data-stu-id="9a4a8-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a4a8-143">Enthält einen Link zur Datenschutzseite oder-Ansicht, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="9a4a8-144">Wichtige Cookies</span><span class="sxs-lookup"><span data-stu-id="9a4a8-144">Essential cookies</span></span>

<span data-ttu-id="9a4a8-145">Wenn die Zustimmung zum Speichern von Cookies nicht angegeben wurde, werden nur Cookies, die als unverzichtbar gekennzeichnet sind, an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="9a4a8-146">Mit dem folgenden Code wird ein Cookie benötigt:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="9a4a8-147">TempData-Anbieter und Sitzungs Zustands Cookies sind nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="9a4a8-148">Das Cookie des [TempData-Anbieters](xref:fundamentals/app-state#tempdata) ist nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="9a4a8-149">Wenn die Nachverfolgung deaktiviert ist, ist der TempData-Anbieter nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9a4a8-150">Um den TempData-Anbieter zu aktivieren, wenn die Nachverfolgung deaktiviert ist, markieren Sie den `Startup.ConfigureServices`TempData-Cookie in:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9a4a8-151">[Sitzungs Zustands](xref:fundamentals/app-state) Cookies sind nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="9a4a8-152">Der Sitzungszustand ist nicht funktionsfähig, wenn die Überwachung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9a4a8-153">Der folgende Code macht Sitzungs Cookies unverzichtbar:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9a4a8-154">Personenbezogene Daten</span><span class="sxs-lookup"><span data-stu-id="9a4a8-154">Personal data</span></span>

<span data-ttu-id="9a4a8-155">ASP.net Core apps, die mit einzelnen Benutzerkonten erstellt wurden, enthalten Code zum herunterladen und Löschen von persönlichen Daten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9a4a8-156">Wählen Sie den Benutzernamen aus, und wählen Sie dann **persönliche Daten**aus:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-156">Select the user name and then select **Personal data**:</span></span>

![Seite "persönliche Daten verwalten"](gdpr/_static/pd.png)

<span data-ttu-id="9a4a8-158">Anmerkungen:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-158">Notes:</span></span>

* <span data-ttu-id="9a4a8-159">Informationen zum Generieren `Account/Manage` des Codes finden Sie unter [Gerüst Identität](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="9a4a8-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9a4a8-160">Die Links zum **Löschen** und **herunterladen** wirken sich nur auf die Standard Identitätsdaten aus.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9a4a8-161">Apps, die benutzerdefinierte Benutzerdaten erstellen, müssen erweitert werden, um die benutzerdefinierten Benutzerdaten zu löschen/herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9a4a8-162">Weitere Informationen finden Sie unter [hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten in der Identität](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="9a4a8-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9a4a8-163">Gespeicherte Token für den Benutzer, die in der Identitätsdaten Bank Tabelle `AspNetUserTokens` gespeichert sind, werden gelöscht, wenn der Benutzer aufgrund des [Fremd Schlüssels](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)über das kaskadierende Lösch Verhalten gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="9a4a8-164">Die [Authentifizierung externer Anbieter](xref:security/authentication/social/index), wie Facebook und Google, ist nicht verfügbar, bevor die Cookie-Richtlinie akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9a4a8-165">Verschlüsselung ruhender Daten</span><span class="sxs-lookup"><span data-stu-id="9a4a8-165">Encryption at rest</span></span>

<span data-ttu-id="9a4a8-166">Einige Datenbanken und Speicher Mechanismen ermöglichen die Verschlüsselung ruhender Daten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9a4a8-167">Verschlüsselung ruhender Daten:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-167">Encryption at rest:</span></span>

* <span data-ttu-id="9a4a8-168">Verschlüsselte Daten werden automatisch verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9a4a8-169">Verschlüsselt, ohne Konfiguration, Programmierung oder andere Aufgaben für die Software, die auf die Daten zugreift.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9a4a8-170">Ist die einfachste und sicherste Option.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9a4a8-171">Ermöglicht der Datenbank die Verwaltung von Schlüsseln und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9a4a8-172">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-172">For example:</span></span>

* <span data-ttu-id="9a4a8-173">Microsoft SQL und Azure SQL bieten [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="9a4a8-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9a4a8-174">Die Datenbank wird von SQL Azure standardmäßig verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9a4a8-175">[Azure-blobdateien,-Dateien,-Tabellen und-Queue Storage werden standardmäßig verschlüsselt](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="9a4a8-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9a4a8-176">Bei Datenbanken, die keine integrierte Verschlüsselung im Ruhezustand bereitstellen, können Sie die Datenträger Verschlüsselung möglicherweise verwenden, um denselben Schutz zu gewährleisten.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9a4a8-177">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-177">For example:</span></span>

* [<span data-ttu-id="9a4a8-178">BitLocker für Windows Server</span><span class="sxs-lookup"><span data-stu-id="9a4a8-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9a4a8-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="9a4a8-179">Linux:</span></span>
  * [<span data-ttu-id="9a4a8-180">ecryptfs</span><span class="sxs-lookup"><span data-stu-id="9a4a8-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9a4a8-181">[EncFS](https://github.com/vgough/encfs)Ein.</span><span class="sxs-lookup"><span data-stu-id="9a4a8-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a4a8-182">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9a4a8-182">Additional resources</span></span>

* [<span data-ttu-id="9a4a8-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9a4a8-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="9a4a8-184">Dsgvo: Hinzufügen einer Schaltfläche zum Widerrufen der Zustimmung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9a4a8-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
