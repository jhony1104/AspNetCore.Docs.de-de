---
title: Veröffentlichen einer ASP.NET Core SignalR-app in Azure-Web-App
author: bradygaster
description: Veröffentlichen einer ASP.NET Core SignalR-app in Azure-Web-App
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 1c472711a86edae8dc6e207734aa54e48c02d47d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087701"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a><span data-ttu-id="577b0-103">Veröffentlichen einer ASP.NET Core SignalR-app zu einer Azure-Web-App</span><span class="sxs-lookup"><span data-stu-id="577b0-103">Publish an ASP.NET Core SignalR app to an Azure Web App</span></span>

<span data-ttu-id="577b0-104">[Azure-Web-App](/azure/app-service/app-service-web-overview) ist eine [Microsoft cloud-computing](https://azure.microsoft.com/) Plattformdienst zum Hosten von Web-apps, einschließlich ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="577b0-104">[Azure Web App](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="577b0-105">In diesem Artikel bezieht sich auf eine ASP.NET Core SignalR-app aus Visual Studio veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="577b0-105">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="577b0-106">Besuchen Sie [SignalR-Diensts für Azure](https://azure.microsoft.com/services/signalr-service) für Weitere Informationen zur Verwendung von SignalR in Azure.</span><span class="sxs-lookup"><span data-stu-id="577b0-106">Visit [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service) for more information about using SignalR on Azure.</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="577b0-107">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="577b0-107">Publish the app</span></span>

<span data-ttu-id="577b0-108">Visual Studio bietet integrierte Tools für die Veröffentlichung in einer Azure-Web-App.</span><span class="sxs-lookup"><span data-stu-id="577b0-108">Visual Studio provides built-in tools for publishing to an Azure Web App.</span></span> <span data-ttu-id="577b0-109">Visual Studio Code-Benutzer können [Azure-Befehlszeilenschnittstelle](/cli/azure) Befehle aus, um apps in Azure veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="577b0-109">Visual Studio Code user can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="577b0-110">Dieser Artikel behandelt die Veröffentlichung mit den Tools in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="577b0-110">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="577b0-111">Zum Veröffentlichen einer app mithilfe von Azure-Befehlszeilenschnittstelle finden Sie unter [veröffentlichen eine ASP.NET Core-app in Azure mit Befehlszeilentools](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="577b0-111">To publish an app using Azure CLI, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

<span data-ttu-id="577b0-112">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="577b0-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span> <span data-ttu-id="577b0-113">Überprüfen Sie, ob **neu erstellen** aktiviert ist, der **Veröffentlichungsziel** Dialogfeld ein, und wählen Sie **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="577b0-113">Confirm that **Create new** is checked in the **Pick a publish target** dialog, and select **Publish**.</span></span>

![Wählen Sie als Veröffentlichungsziel](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

<span data-ttu-id="577b0-115">Geben Sie die folgende Informationen in den **App Service erstellen** Dialogfeld, und klicken **erstellen**.</span><span class="sxs-lookup"><span data-stu-id="577b0-115">Enter the following information in the **Create App Service** dialog and select **Create**.</span></span>

| <span data-ttu-id="577b0-116">Element</span><span class="sxs-lookup"><span data-stu-id="577b0-116">Item</span></span> | <span data-ttu-id="577b0-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="577b0-117">Description</span></span> |
| ---- | ----------- |
| <span data-ttu-id="577b0-118">**App-name**</span><span class="sxs-lookup"><span data-stu-id="577b0-118">**App name**</span></span> | <span data-ttu-id="577b0-119">Ein eindeutiger Name der app.</span><span class="sxs-lookup"><span data-stu-id="577b0-119">A unique name of the app.</span></span> |
| <span data-ttu-id="577b0-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="577b0-120">**Subscription**</span></span> | <span data-ttu-id="577b0-121">Das Azure-Abonnement, das die app verwendet.</span><span class="sxs-lookup"><span data-stu-id="577b0-121">The Azure subscription that the app uses.</span></span> |
| <span data-ttu-id="577b0-122">**Ressourcengruppe**</span><span class="sxs-lookup"><span data-stu-id="577b0-122">**Resource Group**</span></span> | <span data-ttu-id="577b0-123">Die Gruppe von zugehörigen Ressourcen zu denen die app gehört.</span><span class="sxs-lookup"><span data-stu-id="577b0-123">The group of related resources to which the app belongs.</span></span>  |
| <span data-ttu-id="577b0-124">**Hostingplan**</span><span class="sxs-lookup"><span data-stu-id="577b0-124">**Hosting Plan**</span></span> | <span data-ttu-id="577b0-125">Der Tarif für die Web-app.</span><span class="sxs-lookup"><span data-stu-id="577b0-125">The pricing plan for the web app.</span></span> |

![App Service erstellen](publish-to-azure-web-app/_static/create-app-service-dialog.png)

<span data-ttu-id="577b0-127">Visual Studio führt die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="577b0-127">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="577b0-128">Erstellt ein Veröffentlichungsprofil mit veröffentlichungseinstellungen.</span><span class="sxs-lookup"><span data-stu-id="577b0-128">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="577b0-129">Erstellt oder verwendet eine vorhandene *Azure-Web-App* mit den angegebenen Details.</span><span class="sxs-lookup"><span data-stu-id="577b0-129">Creates or uses an existing *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="577b0-130">Die app wird veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="577b0-130">Publishes the app.</span></span>
* <span data-ttu-id="577b0-131">Startet einen Browser mit der veröffentlichten Web-app geladen.</span><span class="sxs-lookup"><span data-stu-id="577b0-131">Launches a browser, with the published web app loaded.</span></span>

<span data-ttu-id="577b0-132">Beachten Sie, dass das Format der URL für die app *app {Name}.azurewebsites.NET".net*.</span><span class="sxs-lookup"><span data-stu-id="577b0-132">Notice the format of the URL for the app is *{app name}.azurewebsites.net*.</span></span> <span data-ttu-id="577b0-133">Beispielsweise eine app namens `SignalRChattR` verfügt über eine URL, der aussieht wie `https://signalrchattr.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="577b0-133">For example, an app named `SignalRChattR` has a URL that looks like `https://signalrchattr.azurewebsites.net`.</span></span>

<span data-ttu-id="577b0-134">Wenn ein HTTP-502.2-Fehler auftritt, finden Sie unter [Bereitstellen von ASP.NET Core Vorschauversion für Azure App Service](xref:host-and-deploy/azure-apps/index) zur Fehlerbehebung.</span><span class="sxs-lookup"><span data-stu-id="577b0-134">If an HTTP 502.2 error occurs, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index) to resolve it.</span></span>

## <a name="configure-signalr-web-app"></a><span data-ttu-id="577b0-135">SignalR-Web-app konfigurieren</span><span class="sxs-lookup"><span data-stu-id="577b0-135">Configure SignalR web app</span></span>

<span data-ttu-id="577b0-136">ASP.NET Core SignalR-apps, die veröffentlicht werden, wie eine Azure-Web-App benötigen [ARR-Affinität](https://en.wikipedia.org/wiki/Application_Request_Routing) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="577b0-136">ASP.NET Core SignalR apps that are published as an Azure Web App must have [ARR Affinity](https://en.wikipedia.org/wiki/Application_Request_Routing) enabled.</span></span> <span data-ttu-id="577b0-137">[WebSockets](xref:fundamentals/websockets) aktiviert sein sollte, um die WebSockets-Übertragung-Funktion zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="577b0-137">[WebSockets](xref:fundamentals/websockets) should be enabled, to allow the WebSockets transport to function.</span></span>

<span data-ttu-id="577b0-138">Navigieren Sie im Azure-Portal zu **Anwendungseinstellungen** für Ihre Web-app.</span><span class="sxs-lookup"><span data-stu-id="577b0-138">In the Azure portal, navigate to **App Settings** for your web app.</span></span> <span data-ttu-id="577b0-139">Legen Sie **WebSockets** zu **auf**, und vergewissern Sie sich **ARR-Affinität** ist **auf**.</span><span class="sxs-lookup"><span data-stu-id="577b0-139">Set **WebSockets** to **On**, and verify **ARR Affinity** is **On**.</span></span>

![Azure Web-app-Einstellungen im Azure-portal](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 <span data-ttu-id="577b0-141">WebSockets und andere Transporte [sind basierend auf den App Service-Plan beschränkt](/azure/azure-subscription-service-limits#app-service-limits).</span><span class="sxs-lookup"><span data-stu-id="577b0-141">WebSockets and other transports [are limited based on the App Service Plan](/azure/azure-subscription-service-limits#app-service-limits).</span></span>

## <a name="related-resources"></a><span data-ttu-id="577b0-142">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="577b0-142">Related resources</span></span>

* [<span data-ttu-id="577b0-143">Veröffentlichen einer ASP.NET Core-Apps in Azure mit Befehlszeilentools</span><span class="sxs-lookup"><span data-stu-id="577b0-143">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="577b0-144">Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="577b0-144">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>](xref:tutorials/publish-to-azure-webapp-using-vs)
* [<span data-ttu-id="577b0-145">Hosten und Bereitstellen von ASP.NET Core-Vorschau-apps in Azure</span><span class="sxs-lookup"><span data-stu-id="577b0-145">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
