---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Mithilfe von Web-API 2 mit Entitätsframework 6 | Microsoft-Dokumentation
author: MikeWasson
description: In diesem Tutorial erfahren Sie, dass die Grundlagen der Erstellung einer Web-Anwendung mit einer ASP.NET Web-API-back-End. Das Lernprogramm verwendet Entity Framework 6 für das Layout der Daten...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236522"
---
<a name="using-web-api-2-with-entity-framework-6"></a>Mithilfe von Web-API 2 mit Entitätsframework 6
====================

[Abgeschlossenes Projekt herunterladen](https://github.com/MikeWasson/BookService)

> In diesem Tutorial erfahren Sie, dass die Grundlagen der Erstellung einer Web-Anwendung mit einer ASP.NET Web-API-back-End. Das Lernprogramm verwendet Entity Framework 6 für die Datenschicht, und klicken Sie auf "Knockout.js", für die clientseitige JavaScript-Anwendung. Das Tutorial veranschaulicht auch zum Bereitstellen der app in Azure App Service-Web-Apps.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
> - Web-API 2.1
> - Visual Studio 2017 (Visual Studio 2017 herunterladen [hier](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))
> - Entity Framework 6
> - .NET 4.7
> - [Knockout.js](http://knockoutjs.com/) 3.1

Dieses Tutorial verwendet ASP.NET Web API 2 mit Entity Framework 6, um eine Webanwendung erstellen, die eine Back-End-Datenbank ändert. Hier ist ein Screenshot der Anwendung, die Sie erstellen.

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

Die app verwendet eine Single-Page-Anwendung (SPA). "Single-Page-Anwendung" ist der allgemeine Begriff für eine Webanwendung, die eine einzelne HTML-Seite lädt, und klicken Sie dann die Seite wird dynamisch aktualisiert, anstatt neue Seiten laden. Nach dem ersten Laden der Seite werden die app mit dem Server über AJAX-Anforderungen. Die AJAX-Anforderungen JSON-Daten zurückgeben, die die app verwendet wird, um die Benutzeroberfläche zu aktualisieren.

AJAX ist nicht neu, aber heute bestehen die JavaScript-Frameworks, die zum Erstellen und Verwalten einer großen anspruchsvollen SPA-Anwendung zu erleichtern. Dieses Tutorial verwendet ["Knockout.js"](http://knockoutjs.com/), aber Sie können alle JavaScript-Clientframework verwenden.

Hier sind die wichtigsten Bausteine für diese app aus:

- ASP.NET MVC erstellt, die HTML-Seite.
- ASP.NET Web-API verarbeitet die AJAX-Anforderungen und JSON-Daten zurückgegeben.
- Knockout.js-Datenbindung der HTML-Elemente, die JSON-Daten.
- Entitätsframework finden Sie in der Datenbank.

## <a name="see-this-app-running-on-azure"></a>Finden Sie unter dieser app in Azure ausführen

Möchten Sie die fertige Website als live-Web-app ausgeführt wird, finden Sie unter? Sie können eine vollständige Version der app beim Azure-Konto bereitstellen, durch die folgende Schaltfläche auswählen.

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

Sie benötigen ein Azure-Konto zum Bereitstellen dieser Lösung in Azure. Wenn Sie nicht bereits über ein Konto verfügen, müssen Sie die folgenden Optionen aus:

- [Öffnen Sie ein Azure-Konto kostenlos](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – Sie erhalten ein Guthaben zum Ausprobieren Zahlungspflichtiger Azure-Dienste nutzen können, und auch nach dem sie verwendet werden, bis können Sie das Konto behalten und kostenlose Azure-Dienste.
- [MSDN-abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -Ihr MSDN-Abonnement ein monatliches Guthaben, die Sie für zahlungspflichtige Azure-Dienste verwenden können.

## <a name="create-the-project"></a>Erstellen eines Projekts

Öffnen Sie Visual Studio. Von der **Datei** , wählen Sie im Menü **neu**, und wählen Sie dann **Projekt**. (Oder wählen Sie **neues Projekt** auf der Startseite.)

In der **neues Projekt** wählen Sie im Dialogfeld **Web** im linken Bereich und **ASP.NET-Webanwendung ((.NET Framework)** im mittleren Bereich. Nennen Sie das Projekt **BookService** , und wählen Sie **OK**.

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

In der **neues ASP.NET-Projekt** wählen Sie im Dialogfeld die **Web-API-** Vorlage.

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


Klicken Sie auf **OK**, um das Projekt zu erstellen.

## <a name="configure-azure-settings-optional"></a>Konfigurieren von Azure-Einstellungen (optional)

Nachdem Sie das Projekt erstellt haben, können Sie jederzeit auf Azure App Service-Web-Apps bereitstellen. 

1. Projektmappen-Explorer mit der Maustaste auf Ihr Projekt, und wählen Sie **veröffentlichen**.

2. Wählen Sie im Fenster, das angezeigt wird, **starten**. Die **Veröffentlichungsziel** Dialogfeld wird angezeigt.

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. Klicken Sie auf **Profil erstellen**. Das Dialogfeld **App Service erstellen** wird angezeigt.

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   Akzeptieren Sie die Standardeinstellungen, oder geben Sie unterschiedliche Werte für den Anwendungsnamen, die Ressourcengruppe, die hosting-Plan, Azure-Abonnement und geografischen Region. 

4. Wählen Sie **erstellen Sie eine SQL­Datenbank**. Die **Konfigurieren von SQL Server** Dialogfeld wird angezeigt. 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   Akzeptieren Sie die Standardeinstellungen, oder geben Sie andere Werte ein. Geben Sie eine **Adminstrator Username** und **Administratorkennwort** für Ihre neue Datenbank. Wählen Sie **OK** Wenn Sie fertig sind. Die **App Service erstellen** Seite wird erneut angezeigt.

5. Wählen Sie **erstellen** auf Ihr Profil zu erstellen. In der unteren rechten Ecke, der angibt, dass die Bereitstellung ausgeführt wird, wird eine Meldung angezeigt. Nach einer kurzen Zeit die **veröffentlichen** Fenster wird erneut angezeigt.

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    Das Profil, das Sie erstellt haben, zum Bereitstellen der app ist jetzt verfügbar. 


> [!div class="step-by-step"]
> [Nächste](part-2.md)
