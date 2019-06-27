---
title: Veröffentlichen einer ASP.NET Core SignalR-app in Azure App Service
author: bradygaster
description: Erfahren Sie, wie Sie eine ASP.NET Core SignalR-app in Azure App Service veröffentlichen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406111"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="41ade-103">Veröffentlichen einer ASP.NET Core SignalR-app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="41ade-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="41ade-104">Durch [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="41ade-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="41ade-105">[Azure App Service](/azure/app-service/app-service-web-overview) ist eine [Microsoft cloud-computing](https://azure.microsoft.com/) Plattformdienst zum Hosten von Web-apps, einschließlich ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41ade-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="41ade-106">In diesem Artikel bezieht sich auf eine ASP.NET Core SignalR-app aus Visual Studio veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="41ade-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="41ade-107">Weitere Informationen finden Sie unter [SignalR-Diensts für Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="41ade-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="41ade-108">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="41ade-108">Publish the app</span></span>

<span data-ttu-id="41ade-109">Dieser Artikel behandelt die Veröffentlichung mit den Tools in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="41ade-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="41ade-110">Benutzer von Visual Studio Code können [Azure-Befehlszeilenschnittstelle](/cli/azure) Befehle aus, um apps in Azure veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="41ade-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="41ade-111">Weitere Informationen finden Sie unter [veröffentlichen eine ASP.NET Core-app in Azure mit Befehlszeilentools](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="41ade-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="41ade-112">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="41ade-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="41ade-113">Überprüfen Sie, ob **App Service** und **neu erstellen** Auswahl in der **Veröffentlichungsziel** Dialogfeld.</span><span class="sxs-lookup"><span data-stu-id="41ade-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="41ade-114">Wählen Sie **Profil erstellen** aus der **veröffentlichen** löschen Sie die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="41ade-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="41ade-115">Geben Sie die Informationen in der folgenden Tabelle beschriebenen die **App Service erstellen** Dialogfeld, und klicken **erstellen**.</span><span class="sxs-lookup"><span data-stu-id="41ade-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="41ade-116">Element</span><span class="sxs-lookup"><span data-stu-id="41ade-116">Item</span></span>               | <span data-ttu-id="41ade-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="41ade-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="41ade-118">**Name**</span><span class="sxs-lookup"><span data-stu-id="41ade-118">**Name**</span></span>           | <span data-ttu-id="41ade-119">Eindeutiger Name der app.</span><span class="sxs-lookup"><span data-stu-id="41ade-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="41ade-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="41ade-120">**Subscription**</span></span>   | <span data-ttu-id="41ade-121">Azure-Abonnement, die die app verwendet.</span><span class="sxs-lookup"><span data-stu-id="41ade-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="41ade-122">**Ressourcengruppe**</span><span class="sxs-lookup"><span data-stu-id="41ade-122">**Resource Group**</span></span> | <span data-ttu-id="41ade-123">Gruppe von zugehörigen Ressourcen zu denen die app gehört.</span><span class="sxs-lookup"><span data-stu-id="41ade-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="41ade-124">**Hostingplan**</span><span class="sxs-lookup"><span data-stu-id="41ade-124">**Hosting Plan**</span></span>   | <span data-ttu-id="41ade-125">Der Tarif für die Web-app.</span><span class="sxs-lookup"><span data-stu-id="41ade-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="41ade-126">Wählen Sie die **Azure SignalR Service** in die **Abhängigkeiten** > **hinzufügen** Dropdown-Liste:</span><span class="sxs-lookup"><span data-stu-id="41ade-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Dependencies-Bereich mit der Auswahl der Azure SignalR Service in der Liste der Dropdown-Liste hinzufügen](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="41ade-128">In der **Azure SignalR Service** wählen Sie im Dialogfeld **erstellen Sie eine neue Azure SignalR Service-Instanz**.</span><span class="sxs-lookup"><span data-stu-id="41ade-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="41ade-129">Geben Sie einen **Namen**, **Ressourcengruppe**, und **Speicherort**.</span><span class="sxs-lookup"><span data-stu-id="41ade-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="41ade-130">Wechseln Sie zurück zur der **Azure SignalR Service** Dialogfeld, und klicken **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="41ade-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="41ade-131">Visual Studio führt die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="41ade-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="41ade-132">Erstellt ein Veröffentlichungsprofil mit veröffentlichungseinstellungen.</span><span class="sxs-lookup"><span data-stu-id="41ade-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="41ade-133">Erstellt eine *Azure-Web-App* mit den angegebenen Details.</span><span class="sxs-lookup"><span data-stu-id="41ade-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="41ade-134">Die app wird veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="41ade-134">Publishes the app.</span></span>
* <span data-ttu-id="41ade-135">Startet einen Browser, der die Web-app lädt.</span><span class="sxs-lookup"><span data-stu-id="41ade-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="41ade-136">Das Format der URL der app ist `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="41ade-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="41ade-137">Beispielsweise eine app namens `SignalRChatApp` verfügt über eine URL von `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="41ade-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="41ade-138">Wenn eine HTTP *502.2 - aufgrund eines ungültigen Gateways* Fehler tritt auf, beim Bereitstellen einer app, die eine Vorschauversion von .NET Core-Version abzielt, finden Sie unter [Bereitstellen von ASP.NET Core Vorschauversion für Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) zur Fehlerbehebung.</span><span class="sxs-lookup"><span data-stu-id="41ade-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="41ade-139">Konfigurieren Sie die app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="41ade-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="41ade-140">*In diesem Abschnitt gelten nur für apps, die nicht mit dem Azure SignalR-Dienst.*</span><span class="sxs-lookup"><span data-stu-id="41ade-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="41ade-141">Wenn die app über den Azure SignalR Service verwendet, erforderlich keine App Service die Konfiguration der Affinität für Application Request Routing (ARR) und Web Sockets, die in diesem Abschnitt beschriebenen.</span><span class="sxs-lookup"><span data-stu-id="41ade-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="41ade-142">Clients eine Verbindung herstellen ihrer Web-Sockets, um den Azure SignalR Service, nicht direkt an die app.</span><span class="sxs-lookup"><span data-stu-id="41ade-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="41ade-143">Für apps, die ohne den Azure SignalR Service gehostet werden zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="41ade-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="41ade-144">[ARR-Affinität](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) zum Weiterleiten von Anforderungen von einem Benutzer an der gleichen App Service-Instanz.</span><span class="sxs-lookup"><span data-stu-id="41ade-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="41ade-145">Die Standardeinstellung ist **auf**.</span><span class="sxs-lookup"><span data-stu-id="41ade-145">The default setting is **On**.</span></span>
* <span data-ttu-id="41ade-146">[WebSockets](xref:fundamentals/websockets) um das WebSockets-Transport-Funktion zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="41ade-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="41ade-147">Die Standardeinstellung ist **aus**.</span><span class="sxs-lookup"><span data-stu-id="41ade-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="41ade-148">Navigieren Sie im Azure-Portal zu der Web-app in **Anwendungsdienste**.</span><span class="sxs-lookup"><span data-stu-id="41ade-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="41ade-149">Open **Konfiguration** > **Allgemeine Einstellungen**.</span><span class="sxs-lookup"><span data-stu-id="41ade-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="41ade-150">Legen Sie **WebSockets** zu **auf**.</span><span class="sxs-lookup"><span data-stu-id="41ade-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="41ade-151">Überprüfen Sie, ob **ARR-Affinität** nastaven NA hodnotu **auf**.</span><span class="sxs-lookup"><span data-stu-id="41ade-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="41ade-152">App Service-Plans begrenzt</span><span class="sxs-lookup"><span data-stu-id="41ade-152">App Service Plan limits</span></span>

<span data-ttu-id="41ade-153">WebSockets und andere Transporte sind beschränkt basierend auf den App Service-Plan ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="41ade-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="41ade-154">Weitere Informationen finden Sie unter den *Grenzwerte für Azure-Clouddienste* und *App Service-limits* Teile der [Azure-Abonnements und diensteinschränkungen, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) Artikel.</span><span class="sxs-lookup"><span data-stu-id="41ade-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41ade-155">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="41ade-155">Additional resources</span></span>

* [<span data-ttu-id="41ade-156">Was ist Azure SignalR Service?</span><span class="sxs-lookup"><span data-stu-id="41ade-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="41ade-157">Veröffentlichen einer ASP.NET Core-Apps in Azure mit Befehlszeilentools</span><span class="sxs-lookup"><span data-stu-id="41ade-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="41ade-158">Hosten und Bereitstellen von ASP.NET Core-Vorschau-apps in Azure</span><span class="sxs-lookup"><span data-stu-id="41ade-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
