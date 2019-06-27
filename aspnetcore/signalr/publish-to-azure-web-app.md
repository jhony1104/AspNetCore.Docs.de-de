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
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Veröffentlichen einer ASP.NET Core SignalR-app in Azure App Service

Durch [Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) ist eine [Microsoft cloud-computing](https://azure.microsoft.com/) Plattformdienst zum Hosten von Web-apps, einschließlich ASP.NET Core.

> [!NOTE]
> In diesem Artikel bezieht sich auf eine ASP.NET Core SignalR-app aus Visual Studio veröffentlichen. Weitere Informationen finden Sie unter [SignalR-Diensts für Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Veröffentlichen der App

Dieser Artikel behandelt die Veröffentlichung mit den Tools in Visual Studio. Benutzer von Visual Studio Code können [Azure-Befehlszeilenschnittstelle](/cli/azure) Befehle aus, um apps in Azure veröffentlichen. Weitere Informationen finden Sie unter [veröffentlichen eine ASP.NET Core-app in Azure mit Befehlszeilentools](/azure/app-service/app-service-web-get-started-dotnet).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Überprüfen Sie, ob **App Service** und **neu erstellen** Auswahl in der **Veröffentlichungsziel** Dialogfeld.

1. Wählen Sie **Profil erstellen** aus der **veröffentlichen** löschen Sie die Schaltfläche.

   Geben Sie die Informationen in der folgenden Tabelle beschriebenen die **App Service erstellen** Dialogfeld, und klicken **erstellen**.

   | Element               | Beschreibung |
   | ------------------ | ----------- |
   | **Name**           | Eindeutiger Name der app. |
   | **Abonnement**   | Azure-Abonnement, die die app verwendet. |
   | **Ressourcengruppe** | Gruppe von zugehörigen Ressourcen zu denen die app gehört. |
   | **Hostingplan**   | Der Tarif für die Web-app. |

1. Wählen Sie die **Azure SignalR Service** in die **Abhängigkeiten** > **hinzufügen** Dropdown-Liste:

   ![Dependencies-Bereich mit der Auswahl der Azure SignalR Service in der Liste der Dropdown-Liste hinzufügen](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. In der **Azure SignalR Service** wählen Sie im Dialogfeld **erstellen Sie eine neue Azure SignalR Service-Instanz**.

1. Geben Sie einen **Namen**, **Ressourcengruppe**, und **Speicherort**. Wechseln Sie zurück zur der **Azure SignalR Service** Dialogfeld, und klicken **hinzufügen**.

Visual Studio führt die folgenden Aufgaben aus:

* Erstellt ein Veröffentlichungsprofil mit veröffentlichungseinstellungen.
* Erstellt eine *Azure-Web-App* mit den angegebenen Details.
* Die app wird veröffentlicht.
* Startet einen Browser, der die Web-app lädt.

Das Format der URL der app ist `{APP SERVICE NAME}.azurewebsites.net`. Beispielsweise eine app namens `SignalRChatApp` verfügt über eine URL von `https://signalrchatapp.azurewebsites.net`.

Wenn eine HTTP *502.2 - aufgrund eines ungültigen Gateways* Fehler tritt auf, beim Bereitstellen einer app, die eine Vorschauversion von .NET Core-Version abzielt, finden Sie unter [Bereitstellen von ASP.NET Core Vorschauversion für Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) zur Fehlerbehebung.

## <a name="configure-the-app-in-azure-app-service"></a>Konfigurieren Sie die app in Azure App Service

> [!NOTE]
> *In diesem Abschnitt gelten nur für apps, die nicht mit dem Azure SignalR-Dienst.*
>
> Wenn die app über den Azure SignalR Service verwendet, erforderlich keine App Service die Konfiguration der Affinität für Application Request Routing (ARR) und Web Sockets, die in diesem Abschnitt beschriebenen. Clients eine Verbindung herstellen ihrer Web-Sockets, um den Azure SignalR Service, nicht direkt an die app.

Für apps, die ohne den Azure SignalR Service gehostet werden zu aktivieren:

* [ARR-Affinität](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) zum Weiterleiten von Anforderungen von einem Benutzer an der gleichen App Service-Instanz. Die Standardeinstellung ist **auf**.
* [WebSockets](xref:fundamentals/websockets) um das WebSockets-Transport-Funktion zu ermöglichen. Die Standardeinstellung ist **aus**.

1. Navigieren Sie im Azure-Portal zu der Web-app in **Anwendungsdienste**.
1. Open **Konfiguration** > **Allgemeine Einstellungen**.
1. Legen Sie **WebSockets** zu **auf**.
1. Überprüfen Sie, ob **ARR-Affinität** nastaven NA hodnotu **auf**.

## <a name="app-service-plan-limits"></a>App Service-Plans begrenzt

WebSockets und andere Transporte sind beschränkt basierend auf den App Service-Plan ausgewählt. Weitere Informationen finden Sie unter den *Grenzwerte für Azure-Clouddienste* und *App Service-limits* Teile der [Azure-Abonnements und diensteinschränkungen, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) Artikel.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Was ist Azure SignalR Service?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Veröffentlichen einer ASP.NET Core-Apps in Azure mit Befehlszeilentools](/azure/app-service/app-service-web-get-started-dotnet)
* [Hosten und Bereitstellen von ASP.NET Core-Vorschau-apps in Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
