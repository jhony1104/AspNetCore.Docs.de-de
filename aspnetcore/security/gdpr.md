---
title: Datenschutz-Grundverordnung (DSGVO)-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie auf die DSGVO-Erweiterungspunkte in einer ASP.NET Core-Web-app zugreifen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: security/gdpr
ms.openlocfilehash: 967f3246836c93a1af56f7109edb056220606b58
ms.sourcegitcommit: c716ea9155a6b404c1f3d3d34e2388454cd276d7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716347"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="bda75-103">Europa Allgemein Datenschutz-Grundverordnung (DSGVO)-Unterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bda75-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="bda75-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bda75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bda75-105">ASP.NET Core bietet APIs und Vorlagen, um einige der erfüllen die [Europa Allgemein Datenschutz-Grundverordnung (DSGVO)](https://www.eugdpr.org/) Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="bda75-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="bda75-106">Die Projektvorlagen enthalten Erweiterungspunkte und liegenden ereignisfelds Markup, das Sie durch den Datenschutz und Cookies verwenden von Richtlinien ersetzen können.</span><span class="sxs-lookup"><span data-stu-id="bda75-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="bda75-107">Ein Cookie Zustimmung-Feature können Sie Ihre Zustimmung anfordern (und Nachverfolgen) von Ihren Benutzern zum Speichern von persönlichen Informationen.</span><span class="sxs-lookup"><span data-stu-id="bda75-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="bda75-108">Wenn ein Benutzer noch nicht, um die Datensammlung zugestimmt und die app verfügt über [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) festgelegt `true`, nicht unbedingt Cookies werden nicht an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="bda75-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="bda75-109">Cookies können als wichtig markiert werden.</span><span class="sxs-lookup"><span data-stu-id="bda75-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="bda75-110">Wichtige Cookies werden an den Browser gesendet, selbst wenn der Benutzer noch nicht zugestimmt hat und nachverfolgung ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="bda75-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="bda75-111">[TempData und Sitzungscookies](#tempdata) nicht funktionsfähig sind, bei der Überwachung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="bda75-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="bda75-112">Die [identitätsverwaltung](#pd) Seite enthält einen Link zum Herunterladen und Löschen von Daten des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="bda75-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="bda75-113">Die [Beispiel-app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) können Sie testen, die meisten der DSGVO Erweiterungspunkte und APIs hinzugefügt, um die Vorlagen für ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="bda75-113">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="bda75-114">Finden Sie unter den [Infodatei](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) -Datei zum Testen von Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="bda75-114">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="bda75-115">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bda75-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="bda75-116">ASP.NET Core DSGVO unterstützen, in der Vorlage generierten Codes</span><span class="sxs-lookup"><span data-stu-id="bda75-116">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="bda75-117">Razor-Seiten und MVC-Projekte erstellt, die mit den Projektvorlagen enthalten die folgende DSGVO-Unterstützung:</span><span class="sxs-lookup"><span data-stu-id="bda75-117">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="bda75-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) und [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) in festgelegt sind das `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="bda75-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="bda75-119">Die  *\_CookieConsentPartial.cshtml* [Teilansicht](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bda75-119">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="bda75-120">Ein **Accept** Schaltfläche befindet sich in dieser Datei.</span><span class="sxs-lookup"><span data-stu-id="bda75-120">An **Accept** button is included in this file.</span></span> <span data-ttu-id="bda75-121">Klickt der Benutzer die **Accept** Schaltfläche, die zum Speichern von Cookies zustimmen wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bda75-121">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="bda75-122">Die *Pages/Privacy.cshtml* Seite oder *Views/Home/Privacy.cshtml* Ansicht bietet eine Seite Ihrer Website-Datenschutzrichtlinie ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="bda75-122">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="bda75-123">Die  *\_CookieConsentPartial.cshtml* -Datei generiert einen Link zu die "Datenschutz".</span><span class="sxs-lookup"><span data-stu-id="bda75-123">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="bda75-124">Für apps, die mit individuellen Benutzerkonten erstellt werden, bietet der Seite "verwalten" Links zum Herunterladen und Löschen von [persönliche Benutzerdaten](#pd).</span><span class="sxs-lookup"><span data-stu-id="bda75-124">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="bda75-125">CookiePolicyOptions und UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="bda75-125">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="bda75-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) initialisiert werden `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bda75-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="bda75-127">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) unter "lautet" `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="bda75-127">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="bda75-128">\_CookieConsentPartial.cshtml Teilansicht</span><span class="sxs-lookup"><span data-stu-id="bda75-128">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="bda75-129">Die  *\_CookieConsentPartial.cshtml* Teilansicht:</span><span class="sxs-lookup"><span data-stu-id="bda75-129">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="bda75-130">Diese partielle:</span><span class="sxs-lookup"><span data-stu-id="bda75-130">This partial:</span></span>

* <span data-ttu-id="bda75-131">Ruft den Status der Überwachung für den Benutzer ab.</span><span class="sxs-lookup"><span data-stu-id="bda75-131">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="bda75-132">Wenn die app konfiguriert ist, um die Zustimmung erforderlich ist, muss der Benutzer zustimmen, bevor Cookies nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="bda75-132">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="bda75-133">Wenn Zustimmung erforderlich ist, das Cookie Zustimmung der Bereiche am oberen Rand der Navigationsleiste von erstellten fixiert ist die  *\_Layout.cshtml* Datei.</span><span class="sxs-lookup"><span data-stu-id="bda75-133">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="bda75-134">Stellt eine HTML `<p>` Element, um den Datenschutz und Cookies zusammenzufassen mithilfe der Gruppenrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="bda75-134">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="bda75-135">Enthält einen Link zu "Datenschutz" oder Ansicht, in denen Sie Ihrer Website-Datenschutzrichtlinie beschreiben können.</span><span class="sxs-lookup"><span data-stu-id="bda75-135">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="bda75-136">Wichtige cookies</span><span class="sxs-lookup"><span data-stu-id="bda75-136">Essential cookies</span></span>

<span data-ttu-id="bda75-137">Wenn Zustimmung zum Speichern von Cookies wurde nicht angegeben wurde, nur Cookies, die als wichtig markiert werden an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="bda75-137">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="bda75-138">Im folgenden Code werden einen Cookie wichtig:</span><span class="sxs-lookup"><span data-stu-id="bda75-138">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="bda75-139">TempData-Anbieter und -Status-Sitzungscookies haben keine Bedeutung</span><span class="sxs-lookup"><span data-stu-id="bda75-139">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="bda75-140">Die [TempData-Anbieters](xref:fundamentals/app-state#tempdata) Cookie nicht wichtig ist.</span><span class="sxs-lookup"><span data-stu-id="bda75-140">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="bda75-141">Wenn die Überwachung deaktiviert ist, nicht der TempData-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="bda75-141">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="bda75-142">Um der TempData-Anbieter aktivieren, wenn die nachverfolgung deaktiviert ist, Markieren des TempData-Cookies als wesentlich `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bda75-142">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="bda75-143">[Sitzungsstatus](xref:fundamentals/app-state) Cookies sind nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="bda75-143">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="bda75-144">Sitzungsstatus nicht funktionsfähig, wenn die nachverfolgung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="bda75-144">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="bda75-145">Im folgenden Code werden die Sitzungscookies wichtig:</span><span class="sxs-lookup"><span data-stu-id="bda75-145">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="bda75-146">Personenbezogene Daten</span><span class="sxs-lookup"><span data-stu-id="bda75-146">Personal data</span></span>

<span data-ttu-id="bda75-147">Mit einzelnen Benutzerkonten erstellten ASP.NET Core-apps enthalten Code zum Herunterladen und Löschen von personenbezogenen Daten.</span><span class="sxs-lookup"><span data-stu-id="bda75-147">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="bda75-148">Wählen Sie den Benutzernamen ein, und wählen Sie dann **personenbezogene Daten**:</span><span class="sxs-lookup"><span data-stu-id="bda75-148">Select the user name and then select **Personal data**:</span></span>

![Verwalten personenbezogener Daten (Seite)](gdpr/_static/pd.png)

<span data-ttu-id="bda75-150">Notizen:</span><span class="sxs-lookup"><span data-stu-id="bda75-150">Notes:</span></span>

* <span data-ttu-id="bda75-151">Zum Generieren der `Account/Manage` programmieren, finden Sie unter [Gerüst Identität](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="bda75-151">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="bda75-152">Die **löschen** und **herunterladen** Links dienen nur auf den Standard-Identity-Daten.</span><span class="sxs-lookup"><span data-stu-id="bda75-152">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="bda75-153">Apps, die benutzerdefinierten Daten zu erstellen, müssen zum Löschen und die benutzerdefinierten Benutzerdaten herunterladen erweitert werden.</span><span class="sxs-lookup"><span data-stu-id="bda75-153">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="bda75-154">Weitere Informationen finden Sie unter [hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten Identität](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="bda75-154">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="bda75-155">Gespeicherte Token für den Benutzer, die in der Identity-Datenbanktabelle gespeichert sind `AspNetUserTokens` werden gelöscht, wenn der Benutzer, über das kaskadierende Delete Verhalten aufgrund gelöscht wird der [Fremdschlüssel](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="bda75-155">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="bda75-156">[Authentifizierung des externen Anbieter](xref:security/authentication/social/index), z. B. Facebook und Google, nicht zur Verfügung, bevor die Cookierichtlinie akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="bda75-156">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="bda75-157">Verschlüsselung ruhender Daten</span><span class="sxs-lookup"><span data-stu-id="bda75-157">Encryption at rest</span></span>

<span data-ttu-id="bda75-158">Einige Datenbanken und andere Speichermechanismen ermöglichen die Verschlüsselung im Ruhezustand.</span><span class="sxs-lookup"><span data-stu-id="bda75-158">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="bda75-159">Verschlüsselung ruhender Daten:</span><span class="sxs-lookup"><span data-stu-id="bda75-159">Encryption at rest:</span></span>

* <span data-ttu-id="bda75-160">Gespeicherte Daten verschlüsselt automatisch.</span><span class="sxs-lookup"><span data-stu-id="bda75-160">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="bda75-161">Verschlüsselt, ohne Konfiguration, Programmierung oder andere Aufgaben für die Software, die die Daten zugreift.</span><span class="sxs-lookup"><span data-stu-id="bda75-161">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="bda75-162">Ist die einfachste und sicherste Option.</span><span class="sxs-lookup"><span data-stu-id="bda75-162">Is the easiest and safest option.</span></span>
* <span data-ttu-id="bda75-163">Kann die Datenbank Verwalten von Schlüsseln und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="bda75-163">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="bda75-164">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bda75-164">For example:</span></span>

* <span data-ttu-id="bda75-165">Bereitstellen von Microsoft SQL und Azure SQL [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="bda75-165">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="bda75-166">SQL Azure werden die Datenbank standardmäßig verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="bda75-166">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="bda75-167">[Azure-Blobs, Dateien, Table und Queue Storage standardmäßig verschlüsselt](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="bda75-167">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="bda75-168">Für Datenbanken, die integrierte Verschlüsselung ruhender Daten nicht bereitstellen, können Sie datenträgerverschlüsselung zu verwenden, um den gleichen Schutz bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="bda75-168">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="bda75-169">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bda75-169">For example:</span></span>

* [<span data-ttu-id="bda75-170">BitLocker für WindowsServer</span><span class="sxs-lookup"><span data-stu-id="bda75-170">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="bda75-171">Linux:</span><span class="sxs-lookup"><span data-stu-id="bda75-171">Linux:</span></span>
  * [<span data-ttu-id="bda75-172">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="bda75-172">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="bda75-173">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="bda75-173">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bda75-174">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bda75-174">Additional resources</span></span>

* [<span data-ttu-id="bda75-175">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="bda75-175">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
