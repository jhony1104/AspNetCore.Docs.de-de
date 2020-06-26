---
title: Artikel, die auf ASP.net Core Projekten basieren, die mit einzelnen Benutzerkonten erstellt wurden
author: rick-anderson
description: Entdecken Sie Artikel, die auf ASP.net Core Projekte basieren, die mit einzelnen Benutzerkonten erstellt wurden.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406250"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="34a87-103">Artikel, die auf ASP.net Core Projekten basieren, die mit einzelnen Benutzerkonten erstellt wurden</span><span class="sxs-lookup"><span data-stu-id="34a87-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="34a87-104">ASP.net Core Identity ist in Projektvorlagen in Visual Studio mit der Option "einzelne Benutzerkonten" enthalten.</span><span class="sxs-lookup"><span data-stu-id="34a87-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="34a87-105">Die Authentifizierungs Vorlagen sind in .net Core-CLI mit verfügbar `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="34a87-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="34a87-106">Sehen Sie sich [dieses GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/5833) bei der Web-API-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="34a87-107">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-107">No Authentication</span></span>

<span data-ttu-id="34a87-108">Die Authentifizierung wird im .net Core-CLI mit der `-au` Option angegeben.</span><span class="sxs-lookup"><span data-stu-id="34a87-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="34a87-109">In Visual Studio ist das Dialogfeld **Authentifizierung ändern** für neue Webanwendungen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="34a87-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="34a87-110">Der Standardwert für neue Web-Apps in Visual Studio ist **keine Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="34a87-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="34a87-111">Ohne Authentifizierung erstellte Projekte:</span><span class="sxs-lookup"><span data-stu-id="34a87-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="34a87-112">Es sind keine Webseiten und Benutzeroberflächen zum Anmelden und Abmelden enthalten.</span><span class="sxs-lookup"><span data-stu-id="34a87-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="34a87-113">Keinen Authentifizierungscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="34a87-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="34a87-114">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-114">Windows Authentication</span></span>

<span data-ttu-id="34a87-115">Die Windows-Authentifizierung wird für neue Web-Apps im .net Core-CLI mit der `-au Windows` Option angegeben.</span><span class="sxs-lookup"><span data-stu-id="34a87-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="34a87-116">Im Dialogfeld **Authentifizierung ändern** werden in Visual Studio die Optionen für die **Windows-Authentifizierung** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="34a87-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="34a87-117">Wenn die Windows-Authentifizierung ausgewählt ist, wird die APP für die Verwendung des [IIS-Moduls der Windows-Authentifizierung](xref:host-and-deploy/iis/modules)konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="34a87-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="34a87-118">Die Windows-Authentifizierung ist für Intranetwebsites vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="34a87-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="34a87-119">Optionen für die neue webapp-Authentifizierung in dotnet</span><span class="sxs-lookup"><span data-stu-id="34a87-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="34a87-120">In der folgenden Tabelle sind die für neue Web-Apps verfügbaren Authentifizierungs Optionen aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="34a87-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="34a87-121">Option</span><span class="sxs-lookup"><span data-stu-id="34a87-121">Option</span></span> | <span data-ttu-id="34a87-122">Authentifizierungstyp</span><span class="sxs-lookup"><span data-stu-id="34a87-122">Type of authentication</span></span> | <span data-ttu-id="34a87-123">Link für weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="34a87-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="34a87-124">Keine</span><span class="sxs-lookup"><span data-stu-id="34a87-124">None</span></span>            |  <span data-ttu-id="34a87-125">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-125">No authentication</span></span> | | 
| <span data-ttu-id="34a87-126">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="34a87-126">Individual</span></span>      |  <span data-ttu-id="34a87-127">Individuelle Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="34a87-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="34a87-128">IndividualB2C</span></span>   |  <span data-ttu-id="34a87-129">In der Cloud gehostete individuelle Authentifizierung mit Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="34a87-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="34a87-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="34a87-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="34a87-131">Singleorg</span><span class="sxs-lookup"><span data-stu-id="34a87-131">SingleOrg</span></span>       |  <span data-ttu-id="34a87-132">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="34a87-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="34a87-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="34a87-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="34a87-134">Multiorg</span><span class="sxs-lookup"><span data-stu-id="34a87-134">MultiOrg</span></span>        |  <span data-ttu-id="34a87-135">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="34a87-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="34a87-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="34a87-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="34a87-137">Windows</span><span class="sxs-lookup"><span data-stu-id="34a87-137">Windows</span></span>         |  <span data-ttu-id="34a87-138">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-138">Windows authentication</span></span> | [<span data-ttu-id="34a87-139">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="34a87-140">Visual Studio-Optionen für die neue webapp-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="34a87-141">In der folgenden Tabelle sind die Authentifizierungs Optionen aufgeführt, die beim Erstellen einer neuen Web-App mit Visual Studio verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="34a87-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="34a87-142">Option</span><span class="sxs-lookup"><span data-stu-id="34a87-142">Option</span></span> | <span data-ttu-id="34a87-143">Authentifizierungstyp</span><span class="sxs-lookup"><span data-stu-id="34a87-143">Type of authentication</span></span> | <span data-ttu-id="34a87-144">Link für weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="34a87-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="34a87-145">Keine</span><span class="sxs-lookup"><span data-stu-id="34a87-145">None</span></span>            |  <span data-ttu-id="34a87-146">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-146">No authentication</span></span> | | 
| <span data-ttu-id="34a87-147">Einzelne Benutzerkonten/Benutzerkonten in-APP speichern</span><span class="sxs-lookup"><span data-stu-id="34a87-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="34a87-148">Individuelle Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="34a87-149">Einzelne Benutzerkonten/Verbindung mit einem vorhandenen Benutzerspeicher in der Cloud</span><span class="sxs-lookup"><span data-stu-id="34a87-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="34a87-150">In der Cloud gehostete individuelle Authentifizierung mit Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="34a87-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="34a87-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="34a87-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="34a87-152">Geschäfts-, Schul-oder Uni-Cloud/Single org</span><span class="sxs-lookup"><span data-stu-id="34a87-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="34a87-153">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="34a87-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="34a87-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="34a87-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="34a87-155">Geschäfts-, Schul-oder Uni-Cloud/Multiple org</span><span class="sxs-lookup"><span data-stu-id="34a87-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="34a87-156">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="34a87-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="34a87-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="34a87-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="34a87-158">Windows</span><span class="sxs-lookup"><span data-stu-id="34a87-158">Windows</span></span>         |  <span data-ttu-id="34a87-159">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-159">Windows authentication</span></span> | [<span data-ttu-id="34a87-160">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="34a87-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="34a87-161">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="34a87-161">Additional resources</span></span>

<span data-ttu-id="34a87-162">In den folgenden Artikeln wird gezeigt, wie Sie den in ASP.net Core Vorlagen generierten Code verwenden, der einzelne Benutzerkonten verwendet:</span><span class="sxs-lookup"><span data-stu-id="34a87-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="34a87-163">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="34a87-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="34a87-164">Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="34a87-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
