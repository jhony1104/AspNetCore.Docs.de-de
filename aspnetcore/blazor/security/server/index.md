---
title: Schützen von Blazor Server-Apps von ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen schützen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402311"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="d2a6f-103">Schützen von Blazor Server-Apps von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2a6f-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="d2a6f-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d2a6f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d2a6f-105">Das Schützen von Blazor Server-Apps funktioniert genau wie bei ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="d2a6f-106">Weitere Informationen finden Sie in den Artikeln unter <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="d2a6f-107">Die Themen in dieser Übersicht gelten speziell für Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="d2a6f-108">-Projektvorlage</span><span class="sxs-lookup"><span data-stu-id="d2a6f-108"> project template</span></span>

<span data-ttu-id="d2a6f-109">Die Projektvorlage Blazor Server kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2a6f-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2a6f-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d2a6f-111">Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="d2a6f-112">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="d2a6f-113">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="d2a6f-114">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="d2a6f-114">**No Authentication**</span></span>
* <span data-ttu-id="d2a6f-115">**Einzelne Benutzerkonten**: Benutzerkonten können wie folgt gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="d2a6f-116">Innerhalb der App anhand des Systems [Identity](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="d2a6f-117">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="d2a6f-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="d2a6f-118">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="d2a6f-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="d2a6f-119">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="d2a6f-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2a6f-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2a6f-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2a6f-121">Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="d2a6f-122">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="d2a6f-123">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="d2a6f-123">Authentication mechanism</span></span> | <span data-ttu-id="d2a6f-124">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="d2a6f-125">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="d2a6f-125">`None` (default)</span></span>         | <span data-ttu-id="d2a6f-126">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="d2a6f-127">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d2a6f-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="d2a6f-128">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d2a6f-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="d2a6f-129">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="d2a6f-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="d2a6f-130">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="d2a6f-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="d2a6f-131">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-131">Windows Authentication</span></span> |

<span data-ttu-id="d2a6f-132">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="d2a6f-133">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-133">Create a folder for the project.</span></span>
* <span data-ttu-id="d2a6f-134">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-134">Name the project.</span></span>

<span data-ttu-id="d2a6f-135">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2a6f-136">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d2a6f-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2a6f-137">Befolgen Sie die Anleitungen für Visual Studio für Mac im Artikel <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="d2a6f-138">Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor Server-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="d2a6f-139">Die Anwendung wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core-Identität gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2a6f-140">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d2a6f-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d2a6f-141">Befolgen Sie die Anleitungen für die .NET Core-CLI im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="d2a6f-142">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="d2a6f-143">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="d2a6f-143">Authentication mechanism</span></span> | <span data-ttu-id="d2a6f-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="d2a6f-145">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="d2a6f-145">`None` (default)</span></span>         | <span data-ttu-id="d2a6f-146">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="d2a6f-147">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d2a6f-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="d2a6f-148">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d2a6f-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="d2a6f-149">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="d2a6f-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="d2a6f-150">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="d2a6f-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="d2a6f-151">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d2a6f-151">Windows Authentication</span></span> |

<span data-ttu-id="d2a6f-152">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="d2a6f-153">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-153">Create a folder for the project.</span></span>
* <span data-ttu-id="d2a6f-154">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-154">Name the project.</span></span>

<span data-ttu-id="d2a6f-155">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2a6f-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="d2a6f-156">Gerüst Identity</span><span class="sxs-lookup"><span data-stu-id="d2a6f-156">Scaffold Identity</span></span>

<span data-ttu-id="d2a6f-157">Gerüst Identity in einem Blazor Server-Projekt:</span><span class="sxs-lookup"><span data-stu-id="d2a6f-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="d2a6f-158">[Ohne vorhandene Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="d2a6f-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="d2a6f-159">[Mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="d2a6f-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
