---
uid: mvc/mvc5
title: ASP.NET MVC 5 | Microsoft-Dokumentation
author: rick-anderson
description: ASP.NET MVC 5 ASP.NET MVC 5 ist ein Framework zum Erstellen von skalierbaren, auf Standards basierende Webanwendungen, die mit bewährte Entwurfsmuster und die Leistungsfähigkeit von AS....
ms.author: riande
ms.date: 10/11/2018
ms.assetid: f79fbf7f-59e5-4279-a832-c1a0294630f4
msc.legacyurl: /mvc/mvc5
msc.type: content
ms.openlocfilehash: 14fcf863a4ef5f6c9180cdf9e7b632ccdb1ebcb0
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410468"
---
<a name="aspnet-mvc-5"></a>ASP.NET MVC 5
====================
## <a name="whats-new-in-aspnet-mvc-5"></a>Neuerungen in ASP.NET MVC 5

### <a name="one-aspnet"></a>One ASP.NET

Die Web-MVC-Projektvorlagen nahtlose Integration in die Oberfläche One ASP.NET. Sie können Ihre MVC-Projekt anpassen und Konfigurieren von Authentifizierung unter Verwendung von One ASP.NET projekterstellungs-Assistenten. Ein einführendes Lernprogramm zu ASP.NET MVC 5 finden Sie unter [erste Schritte mit ASP.NET MVC 5](overview/getting-started/introduction/getting-started.md).

Informationen zum Upgrade von MVC 4-Projekte auf MVC 5 finden Sie unter [das Upgrade von einer ASP.NET MVC 4 und Web-API-Projekt auf ASP.NET MVC 5 und Web-API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md).

### <a name="aspnet-identity"></a>ASP.NET Identity

Die MVC-Projektvorlagen wurden aktualisiert, um ASP.NET Identity für Authentifizierung und identitätsverwaltung verwenden. Ein Tutorial mit Facebook und Google-Authentifizierung und der neuen Mitgliedschafts-API finden Sie unter [erstellen Sie eine ASP.NET MVC 5-App mit Facebook und Google OAuth2 und OpenID-Sign-on](overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) und [Bereitstellen einer sicheren ASP.NET MVC-app mit Mitgliedschaft, OAuth und SQL-Datenbank in einer Windows Azure-Website](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).

### <a name="bootstrap"></a>Bootstrap-Stil

MVC-Projektvorlage wurde aktualisiert, um verwenden [Bootstrap](http://getbootstrap.com/) , ein schlankes und reaktionsfähige Aussehen und Verhalten bereitzustellen, die Sie problemlos anpassen können. Weitere Informationen finden Sie unter [Bootstrap in den Projektvorlagen für Visual Studio Web](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap).

### <a name="authentication-filters"></a>Authentifizierungsfilter

[Authentifizierungsfilter](http://www.dotnetcurry.com/showarticle.aspx?ID=957) sind eine neue Art von Filter in ASP.NET MVC, die vor dem Autorisierungsfilter werden also in der ASP.NET MVC-Pipeline ausgeführt und ermöglichen es Ihnen, geben Sie die Authentifizierung Logik pro-Aktion, pro Controller oder global für alle Controller. Authentifizierungsfilter Anmeldeinformationen in der Anforderung zu verarbeiten, und geben einen entsprechenden Prinzipal. Authentifizierungsfilter können authentifizierungsanforderungen auch als Reaktion auf nicht autorisierte Anforderungen hinzufügen. Finden Sie unter [ASP.NET MVC 5-Authentifizierungsfilter](http://www.dotnetcurry.com/showarticle.aspx?ID=957), [Authentifizierungsfilter in ASP.NET MVC 5](http://theshravan.net/blog/authentication-filters-in-asp-net-mvc-5/).

### <a name="filter-overrides"></a>Überschreibungsfilter

Sie können jetzt überschreiben, die Filter auf eine bestimmte Aktionsmethode bzw. einen Controller angewendet wird, durch Angabe einer [Filter überschreiben](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5). Überschreibungsfilter Geben Sie einen Satz von Filtertypen, die für einen bestimmten Bereich (Aktions- oder Controllerebene) nicht ausgeführt werden soll. Dadurch können Sie so konfigurieren Sie Filter, die global angewendet, aber dann bestimmte globale Filter ausschließen, nicht auf bestimmte Aktionen oder Controller angewendet. Finden Sie unter [neue Filterüberschreibungen-Feature in ASP.NET MVC 5 und ASP.NET Web API 2](https://weblogs.asp.net/imranbaloch/archive/2013/09/25/new-filter-overrides-in-asp-net-mvc-5-and-asp-net-web-api-2.aspx), [wie mit der ASP.NET MVC 5 überschreibt Filterfunktion](http://hackwebwith.net/how-to-use-the-asp-net-mvc-5-filter-overrides-feature/), und [Filterüberschreibungen in ASP.NET MVC 5](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5)

### <a name="attribute-routing"></a>Attributrouting

Unterstützt nun das ASP.NET MVC [attributrouting](https://blogs.msdn.com/b/webdev/archive/2013/10/17/attribute-routing-in-asp-net-mvc-5.aspx), Dank der einen Beitrag von Tim McCall der Autor des [AttributeRouting](https://github.com/mccalltd/AttributeRouting). Das attributrouting können Sie Ihre Routen angeben, durch das Hinzufügen von Anmerkungen zu Ihren Aktionen und Controllern.

## <a name="new-web-project-experience"></a>Neue Project-Weboberfläche

Visual Studio erweitert die Erfahrung mit der Erstellung neuer Webprojekte für in Visual Studio 2013 ab. In der **neuen ASP.NET-Webprojekts** Dialogfeld können Sie den Projekttyp, Sie möchten, konfigurieren eine beliebige Kombination aus Technologien (Web Forms, MVC, Web-API), konfigurieren Sie Authentifizierungsoptionen, Docker-Unterstützung hinzufügen und hinzufügen ein Komponententestprojekts, auswählen.

![Neues ASP.NET-Projekt](mvc5/_static/new-aspnet-web-app-dialog.png)

Das Dialogfeld können Sie die standardmäßigen Authentifizierungsoptionen für viele der Vorlagen zu ändern. Z. B. beim Erstellen einer ASP.NET Web Forms-Projekts können Sie eine der folgenden Optionen auswählen:

- Keine Authentifizierung
- Einzelne Benutzerkonten (ASP.NET-Mitgliedschaft oder Anbieter sozialer-Anmeldung)
- Geschäfts-, Schul- oder Unikonten (Active Directory in einer internetanwendung)
- Windows-Authentifizierung (Active Directory in einer Intranetanwendung)

![Authentifizierungsoptionen](mvc5/_static/change-authentication-dialog.png)

Weitere Informationen über den Prozess zum Erstellen von Webprojekten finden Sie unter [Erstellen von ASP.NET-Webprojekten in Visual Studio](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md). Weitere Informationen zu den Authentifizierungsoptionen finden Sie unter [ASP.NET Identity](../identity/overview/index.md).

<a id="scaffold"></a>
### <a name="aspnet-scaffolding"></a>ASP.NET-Gerüstbau

ASP.NET-Gerüstbau ist ein Code-Generierung-Framework für Webanwendungen mit ASP.NET. Es erleichtert die Codebausteine zu Ihrem Projekt hinzufügen, die mit einem Datenmodell interagiert.

In den Vorgängerversionen von Visual Studio 2013 wurde der Gerüstbau auf ASP.NET MVC-Projekte beschränkt. Ab Visual Studio 2013, können Sie Gerüstbau für ASP.NET-Projekten, einschließlich Web Forms verwenden. Visual Studio unterstützt derzeit nicht Generieren von Seiten für eine Web Forms-Projekt, aber Sie können weiterhin Gerüstbau mit Web Forms verwenden, durch das Hinzufügen von MVC-Abhängigkeiten auf das Projekt. Unterstützung für das Generieren von Seiten für Web Forms wird in einer zukünftigen Version hinzugefügt werden.

Wenn Gerüstbau verwenden, alle erforderlichen Abhängigkeiten im Projekt installiert sind. Z. B. Wenn Sie mit einem ASP.NET Web Forms-Projekt beginnen, und klicken Sie dann mithilfe von Gerüstbau einer Web-API-Controller hinzu, werden die erforderlichen NuGet-Pakete und Verweise dem Projekt automatisch hinzugefügt.

MVC-Gerüstbau um eine Web Forms-Projekt hinzuzufügen, fügen einen **neues Gerüstelement** , und wählen Sie **MVC 5-Abhängigkeiten** im Dialogfenster. Es gibt zwei Optionen für den Gerüstbau für MVC. **Nur minimale Abhängigkeiten** und **vollständige Abhängigkeiten**. Bei Auswahl von **nur minimale Abhängigkeiten**, nur die NuGet-Pakete und Verweise für ASP.NET MVC zu Ihrem Projekt hinzugefügt werden. Bei Auswahl von **vollständige Abhängigkeiten**, die mindestens erforderliche Abhängigkeiten sowie die erforderlichen Inhaltsdateien für ein MVC-Projekt hinzugefügt werden.

![Fügen Sie Scaffold-Dialogfeld hinzu "" in Visual Studio](overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

Unterstützung für Async-Controller Gerüstbau verwendet die asynchronen Funktionen von Entity Framework 6.

Weitere Informationen und Lernprogramme finden Sie unter [Gerüstbau-Übersicht über ASP.NET](../visual-studio/overview/2013/aspnet-scaffolding-overview.md).

### <a name="get-help-and-report-issues"></a>Hilfe abrufen und Melden von Problemen

- [Bekannte Probleme und wichtige Änderungsliste](../visual-studio/overview/2013/release-notes.md#knownissues)
- Hier erhalten Sie Hilfe und Erläutern Sie ASP.NET MVC 5 in der [Foren](https://forums.asp.net/1146.aspx)
- [Melden eines Problems in ASP.NET MVC 5](https://github.com/aspnet/AspNetWebStack/issues)
- [Feature anfordern](http://aspnet.uservoice.com/forums/41201-asp-net-mvc)

### <a name="upgrade-from-aspnet-mvc-4"></a>Upgrade von ASP.NET MVC 4

Finden Sie unter [das Upgrade von einer ASP.NET MVC 4 und Web-API-Projekts zum ASP.NET MVC 5 und Web-API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md)
