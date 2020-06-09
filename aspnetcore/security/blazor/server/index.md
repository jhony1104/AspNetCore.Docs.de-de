---
title: Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen absichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454635"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="bb33b-103">Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="bb33b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="bb33b-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb33b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="bb33b-105"> Server-Projektvorlage</span><span class="sxs-lookup"><span data-stu-id="bb33b-105"> Server project template</span></span>

<span data-ttu-id="bb33b-106">Die Projektvorlage „Blazor Server“ kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bb33b-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb33b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb33b-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bb33b-108">Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bb33b-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="bb33b-109">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="bb33b-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="bb33b-110">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="bb33b-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="bb33b-111">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="bb33b-111">**No Authentication**</span></span>
* <span data-ttu-id="bb33b-112">**Einzelne Benutzerkonten**: Benutzerkonten können wie folgt gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="bb33b-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="bb33b-113">Innerhalb der App anhand des Systems [Identity](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb33b-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="bb33b-114">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="bb33b-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="bb33b-115">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="bb33b-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="bb33b-116">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="bb33b-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bb33b-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bb33b-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bb33b-118">Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="bb33b-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="bb33b-119">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="bb33b-120">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="bb33b-120">Authentication mechanism</span></span> | <span data-ttu-id="bb33b-121">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bb33b-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="bb33b-122">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="bb33b-122">`None` (default)</span></span>         | <span data-ttu-id="bb33b-123">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bb33b-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="bb33b-124">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="bb33b-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="bb33b-125">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="bb33b-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="bb33b-126">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="bb33b-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="bb33b-127">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="bb33b-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="bb33b-128">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bb33b-128">Windows Authentication</span></span> |

<span data-ttu-id="bb33b-129">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="bb33b-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="bb33b-130">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-130">Create a folder for the project.</span></span>
* <span data-ttu-id="bb33b-131">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-131">Name the project.</span></span>

<span data-ttu-id="bb33b-132">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb33b-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bb33b-133">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bb33b-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bb33b-134">Befolgen Sie die Anleitungen für Visual Studio für Mac im Artikel <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="bb33b-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="bb33b-135">Wählen Sie im Schritt **Configure your new Blazor Server App** (Ihre neue Blazor Server-App konfigurieren) in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="bb33b-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="bb33b-136">Die Anwendung wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core-Identität gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="bb33b-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bb33b-137">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bb33b-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bb33b-138">Befolgen Sie die Anleitungen für die .NET Core-CLI im Artikel <xref:blazor/get-started>, um ein neues Blazor Blazor-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="bb33b-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="bb33b-139">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="bb33b-140">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="bb33b-140">Authentication mechanism</span></span> | <span data-ttu-id="bb33b-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="bb33b-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="bb33b-142">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="bb33b-142">`None` (default)</span></span>         | <span data-ttu-id="bb33b-143">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bb33b-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="bb33b-144">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="bb33b-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="bb33b-145">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="bb33b-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="bb33b-146">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="bb33b-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="bb33b-147">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="bb33b-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="bb33b-148">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bb33b-148">Windows Authentication</span></span> |

<span data-ttu-id="bb33b-149">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="bb33b-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="bb33b-150">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-150">Create a folder for the project.</span></span>
* <span data-ttu-id="bb33b-151">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bb33b-151">Name the project.</span></span>

<span data-ttu-id="bb33b-152">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb33b-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="bb33b-153">Schützen einer vorhandenen App</span><span class="sxs-lookup"><span data-stu-id="bb33b-153">Secure an existing app</span></span>

<span data-ttu-id="bb33b-154">Das Schützen von Blazor Server-Apps funktioniert genau wie bei ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="bb33b-154">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="bb33b-155">Weitere Informationen finden Sie in den Artikeln unter <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="bb33b-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="bb33b-156">Gerüst Identity</span><span class="sxs-lookup"><span data-stu-id="bb33b-156">Scaffold Identity</span></span>

<span data-ttu-id="bb33b-157">Gerüst Identity in einem Blazor Server-Projekt:</span><span class="sxs-lookup"><span data-stu-id="bb33b-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="bb33b-158">[Ohne vorhandene Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="bb33b-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="bb33b-159">[Mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="bb33b-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
