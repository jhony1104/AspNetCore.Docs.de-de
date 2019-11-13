---
title: Veröffentlichen einer ASP.net Core SignalR-App auf Azure App Service
author: bradygaster
description: Erfahren Sie, wie Sie eine ASP.net Core SignalR-App zum Azure App Service veröffentlichen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963935"
---
# <a name="publish-an-aspnet-core-opno-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="a5cbf-103">Veröffentlichen einer ASP.net Core SignalR-App auf Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a5cbf-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="a5cbf-104">Von [Brady Gastern](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="a5cbf-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="a5cbf-105">[Azure App Service](/azure/app-service/app-service-web-overview) ist ein [Microsoft Cloud Computing](https://azure.microsoft.com/) Platform-Dienst zum Hosting von Web-Apps, einschließlich ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="a5cbf-106">Dieser Artikel bezieht sich auf das Veröffentlichen einer ASP.net Core SignalR-App aus Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="a5cbf-107">Weitere Informationen finden Sie unter [SignalR-Dienst für Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="a5cbf-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="a5cbf-108">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="a5cbf-108">Publish the app</span></span>

<span data-ttu-id="a5cbf-109">In diesem Artikel wird die Veröffentlichung mit den Tools in Visual Studio behandelt.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="a5cbf-110">Visual Studio Code Benutzer können [Azure CLI](/cli/azure) Befehle zum Veröffentlichen von apps in Azure verwenden.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="a5cbf-111">Weitere Informationen finden Sie unter [Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a5cbf-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="a5cbf-112">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="a5cbf-113">Vergewissern Sie sich, dass im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** und **neu erstellen** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="a5cbf-114">Wählen Sie im Dropdown Feld " **veröffentlichen** " die Option **Profil erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="a5cbf-115">Geben Sie die in der folgenden Tabelle beschriebenen Informationen in das Dialogfeld **App Service erstellen** ein, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="a5cbf-116">Element</span><span class="sxs-lookup"><span data-stu-id="a5cbf-116">Item</span></span>               | <span data-ttu-id="a5cbf-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a5cbf-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="a5cbf-118">**Name**</span><span class="sxs-lookup"><span data-stu-id="a5cbf-118">**Name**</span></span>           | <span data-ttu-id="a5cbf-119">Der eindeutige Name der app.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="a5cbf-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="a5cbf-120">**Subscription**</span></span>   | <span data-ttu-id="a5cbf-121">Azure-Abonnement, das von der APP verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="a5cbf-122">**Ressourcengruppe**</span><span class="sxs-lookup"><span data-stu-id="a5cbf-122">**Resource Group**</span></span> | <span data-ttu-id="a5cbf-123">Gruppe verwandter Ressourcen, zu denen die APP gehört.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="a5cbf-124">**Hostingplan**</span><span class="sxs-lookup"><span data-stu-id="a5cbf-124">**Hosting Plan**</span></span>   | <span data-ttu-id="a5cbf-125">Der Tarif für die Web-App.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="a5cbf-126">Wählen Sie in der Dropdown Liste **Abhängigkeiten** > **Hinzufügen** den **Azure-SignalR Dienst** aus:</span><span class="sxs-lookup"><span data-stu-id="a5cbf-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Bereich "Abhängigkeiten" mit der Auswahl von Azure [! Schel. No-Loc (signalr)]-Dienst in der Dropdown Liste "hinzufügen"](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="a5cbf-128">Wählen Sie im Dialogfeld **Azure SignalR-Dienst** die Option **neue Azure SignalR Dienst Instanz erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="a5cbf-129">Geben Sie einen **Namen**, eine **Ressourcengruppe**und einen **Speicherort**an.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="a5cbf-130">Wechseln Sie zurück zum Dialogfeld **Azure SignalR Service** , und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="a5cbf-131">Visual Studio führt die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="a5cbf-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="a5cbf-132">Erstellt ein Veröffentlichungs Profil mit Veröffentlichungs Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="a5cbf-133">Erstellt eine *Azure-Web-App* mit den bereitgestellten Details.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="a5cbf-134">Veröffentlicht die app.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-134">Publishes the app.</span></span>
* <span data-ttu-id="a5cbf-135">Hiermit wird ein Browser gestartet, der die Web-App lädt.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="a5cbf-136">Das Format der App-URL ist `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="a5cbf-137">Eine APP mit dem Namen `SignalRChatApp` hat z. b. eine URL `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="a5cbf-138">Wenn beim Bereitstellen einer APP, die auf eine Vorschauversion von .net Core ausgerichtet ist, ein Fehler vom Typ "http *502,2-* ungültiges Gateway" auftritt, finden Sie weitere Informationen unter Bereitstellen [ASP.net Core Vorschau Release Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)</span><span class="sxs-lookup"><span data-stu-id="a5cbf-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="a5cbf-139">Konfigurieren der app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a5cbf-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="a5cbf-140">*Dieser Abschnitt gilt nur für apps, die nicht den Azure SignalR-Dienst verwenden.*</span><span class="sxs-lookup"><span data-stu-id="a5cbf-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="a5cbf-141">Wenn die APP den Azure SignalR-Dienst verwendet, ist für die APP Service keine Konfiguration der in diesem Abschnitt beschriebenen arr-Affinität (Application Request Routing) und der in diesem Abschnitt beschriebenen websockets erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="a5cbf-142">Clients verbinden ihre websockets mit dem Azure SignalR-Dienst und nicht direkt mit der app.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="a5cbf-143">Aktivieren Sie für apps, die ohne den Azure SignalR Service gehostet werden, Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a5cbf-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="a5cbf-144">[Arr-Affinität](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) zum Weiterleiten von Anforderungen von einem Benutzer an dieselbe App Service Instanz.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="a5cbf-145">Die Standardeinstellung ist **on**.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-145">The default setting is **On**.</span></span>
* <span data-ttu-id="a5cbf-146">[Websockets](xref:fundamentals/websockets) , damit der websockets-Transport funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="a5cbf-147">Die Standardeinstellung ist **Off**.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="a5cbf-148">Navigieren Sie in der Azure-Portal zu der Web-App in **App Services**.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="a5cbf-149">Öffnen Sie die **Konfigurations** > **allgemeinen Einstellungen**.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="a5cbf-150">Legen Sie **websockets** **auf ein**fest.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="a5cbf-151">Vergewissern Sie sich, dass **arr-Affinität** **auf**ein festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="a5cbf-152">App Service Plan Limits</span><span class="sxs-lookup"><span data-stu-id="a5cbf-152">App Service Plan limits</span></span>

<span data-ttu-id="a5cbf-153">Websockets und andere Transporte sind auf Grundlage des ausgewählten App Service Plans beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a5cbf-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="a5cbf-154">Weitere Informationen finden Sie in den Abschnitten *Azure-Cloud Services Limits* und *App Service Limits* im Artikel Einschränkungen für [Azure-Abonnements und Dienste, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="a5cbf-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5cbf-155">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a5cbf-155">Additional resources</span></span>

* <span data-ttu-id="a5cbf-156">[Was ist Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="a5cbf-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a5cbf-157">Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools</span><span class="sxs-lookup"><span data-stu-id="a5cbf-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="a5cbf-158">Hosten und Bereitstellen ASP.net Core Vorschau-apps in Azure</span><span class="sxs-lookup"><span data-stu-id="a5cbf-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
