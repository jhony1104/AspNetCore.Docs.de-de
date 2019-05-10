---
title: Artikel mit einzelnen Benutzerkonten erstellten ASP.NET Core-Projekten
author: rick-anderson
description: Lesen Sie Artikel, die basierend auf ASP.NET Core-Projekte, die mit individuellen Benutzerkonten erstellt.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: f9c1be16386da935382275815bb5fd5c72894b1c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892527"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="4c598-103">Artikel mit einzelnen Benutzerkonten erstellten ASP.NET Core-Projekten</span><span class="sxs-lookup"><span data-stu-id="4c598-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="4c598-104">ASP.NET Core Identity ist in-Projektvorlagen in Visual Studio mit der Option "Einzelne Benutzerkonten" enthalten.</span><span class="sxs-lookup"><span data-stu-id="4c598-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="4c598-105">Die Authentifizierung-Beispielvorlagen stehen im .NET Core-CLI mit `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="4c598-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="4c598-106">Finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/5833) für die Web-API-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4c598-106">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="4c598-107">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="4c598-107">No Authentication</span></span>

<span data-ttu-id="4c598-108">Gibt die Authentifizierung in .NET Core-CLI mit dem `-au` Option.</span><span class="sxs-lookup"><span data-stu-id="4c598-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="4c598-109">In Visual Studio die **Authentifizierung ändern** Dialogfeld für neue Webanwendungen verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="4c598-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="4c598-110">Der Standardwert für neue Web-apps in Visual Studio ist **keine Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="4c598-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="4c598-111">Projekte, die ohne Authentifizierung erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="4c598-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="4c598-112">Enthalten Sie nicht, Web Pages und die Benutzeroberfläche für die Anmeldung, und melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="4c598-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="4c598-113">Enthalten Sie nicht Code für die Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4c598-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="4c598-114">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="4c598-114">Windows Authentication</span></span>

<span data-ttu-id="4c598-115">Windows-Authentifizierung angegeben ist, für die neue Web-apps in .NET Core-CLI mit dem `-au Windows` Option.</span><span class="sxs-lookup"><span data-stu-id="4c598-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="4c598-116">In Visual Studio die **Authentifizierung ändern** Dialogfeld bietet die **Windows-Authentifizierung** Optionen.</span><span class="sxs-lookup"><span data-stu-id="4c598-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="4c598-117">Wenn Windows-Authentifizierung ausgewählt ist, ist die app zur Verwendung konfiguriert die [-Modul für Windows-Authentifizierung IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="4c598-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="4c598-118">Windows-Authentifizierung ist für Intranet-Websites gedacht.</span><span class="sxs-lookup"><span data-stu-id="4c598-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4c598-119">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4c598-119">Additional resources</span></span>

<span data-ttu-id="4c598-120">Die folgenden Artikel zeigen, wie mit den generierten Code in ASP.NET Core-Vorlagen, die einzelne Benutzerkonten zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4c598-120">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="4c598-121">Zweistufige Authentifizierung mit SMS</span><span class="sxs-lookup"><span data-stu-id="4c598-121">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="4c598-122">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4c598-122">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="4c598-123">Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind</span><span class="sxs-lookup"><span data-stu-id="4c598-123">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
