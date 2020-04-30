---
title: Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen absichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206329"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="b282b-103">Absichern von Blazor Server-Apps als ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="b282b-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b282b-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b282b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="b282b-105">Projektvorlage „Blazor Server“</span><span class="sxs-lookup"><span data-stu-id="b282b-105">Blazor Server project template</span></span>

<span data-ttu-id="b282b-106">Die Projektvorlage „Blazor Server“ kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b282b-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b282b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b282b-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b282b-108">Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b282b-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b282b-109">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="b282b-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b282b-110">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="b282b-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b282b-111">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="b282b-111">**No Authentication**</span></span>
* <span data-ttu-id="b282b-112">**Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="b282b-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="b282b-113">Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b282b-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b282b-114">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b282b-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b282b-115">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="b282b-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="b282b-116">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="b282b-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b282b-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b282b-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b282b-118">Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="b282b-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b282b-119">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b282b-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b282b-120">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="b282b-120">Authentication mechanism</span></span> | <span data-ttu-id="b282b-121">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b282b-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b282b-122">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="b282b-122">`None` (default)</span></span>         | <span data-ttu-id="b282b-123">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b282b-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b282b-124">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b282b-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b282b-125">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b282b-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b282b-126">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="b282b-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b282b-127">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="b282b-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b282b-128">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b282b-128">Windows Authentication</span></span> |

<span data-ttu-id="b282b-129">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="b282b-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b282b-130">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b282b-130">Create a folder for the project.</span></span>
* <span data-ttu-id="b282b-131">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b282b-131">Name the project.</span></span>

<span data-ttu-id="b282b-132">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b282b-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b282b-133">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b282b-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b282b-134">Befolgen Sie die Anleitungen für Visual Studio für Mac im Artikel <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="b282b-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="b282b-135">Wählen Sie im Schritt **Configure your new Blazor Server App** (Ihre neue Blazor Server-App konfigurieren) in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="b282b-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b282b-136">Die Anwendung wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core-Identität gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b282b-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b282b-137">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b282b-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b282b-138">Befolgen Sie die Anleitungen für die .NET Core-CLI im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="b282b-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b282b-139">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b282b-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b282b-140">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="b282b-140">Authentication mechanism</span></span> | <span data-ttu-id="b282b-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b282b-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b282b-142">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="b282b-142">`None` (default)</span></span>         | <span data-ttu-id="b282b-143">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b282b-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b282b-144">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b282b-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b282b-145">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b282b-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b282b-146">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="b282b-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b282b-147">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="b282b-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b282b-148">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b282b-148">Windows Authentication</span></span> |

<span data-ttu-id="b282b-149">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="b282b-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b282b-150">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b282b-150">Create a folder for the project.</span></span>
* <span data-ttu-id="b282b-151">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b282b-151">Name the project.</span></span>

<span data-ttu-id="b282b-152">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b282b-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
