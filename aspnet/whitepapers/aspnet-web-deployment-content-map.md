---
uid: whitepapers/aspnet-web-deployment-content-map
title: 'ASP.NET: Webbereitstellung – empfohlene Ressourcen | Microsoft-Dokumentation'
author: rick-anderson
description: Dieses Thema enthält Links zu Dokumentationen, die Ressourcen zum Bereitstellen (veröffentlichen) von ASP.NET web-Anwendungen zu IIS mithilfe von Visual Studio 2010, Visual Web De...
ms.author: riande
ms.date: 03/14/2014
ms.assetid: 58b583cd-c4ab-47a3-8527-8c92c298c91f
msc.legacyurl: /whitepapers/aspnet-web-deployment-content-map
msc.type: content
ms.openlocfilehash: 6df0c9d2f38ad1d39abd62787c600ef80da8e8e0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836220"
---
<a name="aspnet-web-deployment---recommended-resources"></a>ASP.NET: Webbereitstellung – empfohlene Ressourcen
====================
> Dieses Thema enthält Links zu Dokumentationen, die Ressourcen zum Bereitstellen (veröffentlichen) von ASP.NET web-Anwendungen zu IIS mithilfe von Visual Studio 2010, Visual Web Developer 2010 und höheren Versionen.
> 
> Wenn Sie wissen, dass ein tolles Blog Posten, [Stackoverflow](http://stackoverflow.com) Thread oder einen anderen Link, die nützlich wären [senden Sie uns eine e-Mail](mailto:aspnetue@microsoft.com?subject=Deployment%20Content%20Map) mit dem Link.
> 
> > [!NOTE] 
> > 
> > Viele dieser Ressourcen beschreiben Bereitstellungsfunktionen, die sind nur verfügbar, wenn Sie eine aktuelle Version der installieren die [Visual Studio Web Publish Update](https://go.microsoft.com/fwlink/?LinkID=208120). Einige Features sind nur in Visual Studio 2012 oder Visual Studio 2013 verfügbar.


Dieses Thema enthält folgende Abschnitte:

- [Grundlegendes zu Bereitstellungsoptionen für Webprojekte](#understanding)
- [Suchen von hosting-Anbieter für eine ASP.NET-Anwendung](#findinghosting)
- [Bereitstellen einer Webanwendung in Visual Studio](#fromvs)
- [Bereitstellen einer Web-Anwendung erstellen und Installieren von einem Webbereitstellungspaket](#package)
- [Bereitstellen einer Webanwendung mit einem Prozess für die fortlaufende Integration (CI)](#ci)
- [Verwenden der Web.config-Transformationen zum Ändern der Einstellungen in der Web.config-Zieldatei oder die Datei "App.config" während der Bereitstellung](#transforms)
- [Verwenden von Web Deploy Parametern zum Ändern der Einstellungen in der Ziel-Webanwendung während der Bereitstellung](#webdeployparms)
- [Sicherstellen einer Anwendungs ist offline, während der Bereitstellung](#appoffline)
- [Bereitstellen einer Datenbank oder Änderungen in einer Datenbank als Teil der Bereitstellung von Webanwendungen](#databasewithweb)
- [Bereitstellen einer Datenbank getrennt von der Bereitstellung von Webanwendungen](#databaseseparate)
- [Bereitstellen einer Webanwendung, die ASP.NET-Anwendung verwendet Dienste, z.B. die Mitgliedschaft und profilerstellung](#aspnetmembership)
- [Vorkompilieren für die Bereitstellung](#precompiling)
- [Bereitstellen einer Intranet-Web-Anwendung](#intranet)
- [Automatisieren häufige Bereitstellungsaufgaben, die standardmäßig nicht automatisiert werden](#automating)
- [Konfigurieren von Webservern, damit Entwickler Webanwendungen, die Sie mit der Web Deploy bereitstellen können](#configuringservers)
- [Konfigurieren von Servern für einen Hostinganbieter](#hostingprovider)
- [Behandlung von Bereitstellungsproblemen](#troubleshooting)
- [Abrufen von Hilfe mit einer bestimmten Bereitstellung Frage](#gettinghelp)
- [Additional Resources](#additional) (Zusätzliche MSBuild-Ressourcen)


<a id="understanding"></a>


## <a name="understanding-deployment-options-for-web-projects"></a>Grundlegendes zu Bereitstellungsoptionen für Webprojekte

- [Übersicht über die Bereitstellung für Visual Studio und ASP.NET Web](https://msdn.microsoft.com/library/dd394698.aspx) (MSDN).
- [Gewusst wie: Bereitstellen einer Windows Azure-Website](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Erläutert Optionen sowie Links zu Ressourcen für die Bereitstellung von Webprojekten auf Windows Azure-Websites, einschließlich [continuous Delivery](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) (aus automatisierten [quellcodeverwaltung](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control.md)) sowie die Verwendung von Visual Studio.
- [Verbesserungen von Visual Studio 2012 Web Publishing](../visual-studio/overview/2012/visual-studio-2012-web-publishing-improvements.md) (Video von Scott Hanselman).
- [Übersicht über Post für die Bereitstellung in Visual Studio 2010](http://vishaljoshi.blogspot.com/2009/09/overview-post-for-web-deployment-in-vs.html) (Vishal Joshi Blog). Einen älteren Blogbeitrag, aber einige der Visual Studio 2010-Ressourcen, die sie verknüpft, um Informationen zu erhalten, die für Visual Studio 2012 weiterhin relevant sind.


<a id="findinghosting"></a>


## <a name="finding-hosting-providers-for-an-aspnet-application"></a>Suchen von hosting-Anbieter für eine ASP.NET-Anwendung

- [Hosten von ASP.NET](https://asp.net/hosting)


<a id="fromvs"></a>


## <a name="deploying-a-web-application-from-visual-studio"></a>Bereitstellen einer Webanwendung in Visual Studio

- [Gewusst wie: Bereitstellen einer Windows Azure-Website](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Erläutert Optionen sowie Links zu Ressourcen für die Bereitstellung von Webprojekten in Windows Azure-Websites. Enthält einen Abschnitt zur Bereitstellung von Visual Studio.
- [ASP.NET-webbereitstellung mithilfe von Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). Teil 12 tutorialreihe wird gezeigt, wie von Webanwendungen mit SQL Server-Datenbanken bereitgestellt wird. Für die Datenbank verwendet die Bereitstellung der DbDacFx-Anbieter und Entity Framework Code First-Migrationen. Enthält zudem Informationen zu [Umwandlungen für die Datei "Web.config"](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md), [Bereitstellen von einzelnen Dateien](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md#specificfiles), [Bereitstellung über die Befehlszeile](../web-forms/overview/deployment/visual-studio-web-deployment/command-line-deployment.md), und [wie Anpassen der Visual Studio Web Pipeline durch Bearbeiten pubxml-Dateien veröffentlichen](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md). Gilt für alle ASP.NET Web-Projekte, einschließlich Web Forms, MVC und Web-API.)
- [Vorgehensweise: Bereitstellen einer Webanwendungsprojekts mit One-Click-Veröffentlichung in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (Referenzinformationen zu den Visual Studio Web Publish-Assistenten)
- [Bereitstellen einer ASP.NET-Webanwendung mit SQL Server Compact mit Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). Dies ist eine frühere Version von **ASP.NET-webbereitstellung mithilfe von Visual Studio** ganz oben in diesem Abschnitt. Vor allem nützlich für jetzt Informationen zum Bereitstellen von SQL Server Compact-Datenbanken und wie Sie von SQL Server Compact auf eine Vollversion von SQL Server zu migrieren.
- [.NET Multi-Tier-Anwendung mithilfe von Storage-Tabellen, Warteschlangen und Blobs](https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) (Microsoft Azure-Website). 5-Teil der tutorialreihe, veranschaulicht, wie ein MVC-Projekt erstellen und in einem Windows Azure-Cloud-Dienst bereitstellen.


<a id="package"></a>
## <a name="deploying-a-web-application-by-creating-and-installing-a-web-deployment-package"></a>Bereitstellen einer Web-Anwendung erstellen und Installieren von einem Webbereitstellungspaket

- [Vorgehensweise: Erstellen ein Webbereitstellungspakets in Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx) (MSDN).
- [Vorgehensweise: Installieren ein Bereitstellungspakets mit der Datei "Deploy.cmd" erstellt von Visual Studio](https://msdn.microsoft.com/library/ff356104.aspx) (MSDN).
- [Mithilfe von Web Deploy-Pakete zum Bereitstellen in IIS auf dem Entwicklungscomputer und auf einem Host von Drittanbietern](http://sedodream.com/2011/11/08/UsingAWebDeployPackageToDeployToIISOnTheDevBoxAndToAThirdPartyHost.aspx) (Sayed Hashimi-Blog). Gewusst wie: Verwenden Sie zum Installieren eines Bereitstellungspakets in IIS auf dem lokalen Computer und auf ein hosting zu Unternehmen, die IIS-Manager unterstützt die IIS-Manager für die Remoteverwaltung.
- [Erstellen eine Web bereitstellen Paket von Visual Studio 2010](https://www.iis.net/learn/publish/using-web-deploy/building-a-web-deploy-package-from-visual-studio-2010) (IIS.NET-Website). Enthält Anweisungen zum Befehlszeilen-Paket erstellen und installieren.
- [Packen Sie einmal veröffentlichen überall](http://sedodream.com/2012/03/14/PackageWebUpdatedAndVideoBelow.aspx) (Sayed Hashimi-Blog). Stellt ein NuGet-Paket, das den Prozess der Transformation der Datei "Web.config" für zielumgebungen mit mehreren, automatisiert, damit Sie ein Paket mit mehreren Servern bereitstellen können. Siehe auch die [PackageWeb Video](https://www.youtube.com/watch?v=-LvUJFI8CzM) von Sayed Hashimi.

Siehe auch im folgenden Abschnitt.


<a id="ci"></a>


## <a name="deploying-a-web-application-using-a-continuous-integration-ci-process"></a>Bereitstellen einer Webanwendung mit einem Prozess für die fortlaufende Integration (CI)

- [Continuous Integration und Continuous Delivery (erstellen realer Cloud-Apps mit Windows Azure).](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) E-Book-Kapitel, die continuous Integration und continuous Delivery eingeführt werden.
- [Gewusst wie: Bereitstellen einer Windows Azure-Website](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Erläutert die Optionen und Links für Ressourcen für die Bereitstellung von Webprojekten in Windows Azure-Websites. Enthält einen Abschnitt zum Automatisieren von Bereitstellungen aus der quellcodeverwaltung.
- [Bereitstellen von Webanwendungen in Unternehmensszenarien](../web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). 40-Teil der tutorialreihe, veranschaulicht Automatisieren der Bereitstellung in einem CI-Prozess mithilfe von Visual Studio 2010 und Team Foundation Server 2010.
- [In der Microsoft Build-Engine: Mithilfe von MSBuild und Team Foundation-Build von Sayed Hashimi und William Bartholomew](http://msbuildbook.com). Dies ist ein Buch, nicht auf eine Webressource, aber es ist eine wichtige Anleitung aus, für das Erlernen von MSBuild für Szenarien mit fortlaufender Integration zu konfigurieren.
- [MSBuild-Erweiterungspaket](https://github.com/mikefourie/MSBuildExtensionPack). Umfasst die Bereitstellungsaufgaben.
- [Team Foundation Build Customization Guide](https://aka.ms/vsarsolutions). Dokumentation von ALM Rangers zum Einrichten von Team Foundation Server behandelt webbereitstellung, und es enthält Lernprogramme und Videos.
- [SlowCheetah XML transformiert werden, von einem CI-Server](http://sedodream.com/2011/12/12/SlowCheetahXMLTransformsFromACIServer.aspx) (Sayed Hashimi-Blog). Erläutert, wie SlowCheetah, eine Visual Studio-add-in für die Transformation von "App.config" und andere XML-Dateien.

Siehe auch [sicherstellen einer Anwendungs offline ist, während der Bereitstellung](aspnet-web-deployment-content-map.md#appoffline) weiter unten in dieser Seite.


<a id="transforms"></a>


## <a name="using-webconfig-transformations-to-change-settings-in-the-destination-webconfig-file-or-appconfig-file-during-deployment"></a>Verwenden der Web.config-Transformationen zum Ändern der Einstellungen in der Web.config-Zieldatei oder die Datei "App.config" während der Bereitstellung

- [Datei "Web.config" Transformationen](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md).
- [Syntax der Web.config-Transformation für die Bereitstellung von Webprojekten mithilfe von Visual Studio](https://msdn.microsoft.com/library/dd465326.aspx) (MSDN).
- [Web Tools 2012.2 - web.config-Transformation](https://www.youtube.com/watch?v=HdPK8mxpKEI) (YouTube-Video von Sayed Hashimi). Veranschaulicht das Einrichten und eine Vorschau der Web.config-Transformation.
- [Wie deaktiviere ich die Web.config-Transformation?](https://msdn.microsoft.com/library/ee942158.aspx#disable_web_config_transformation) (MSDN).
- [Wann sollte ich Web Deploy-Parameter anstelle von Web.config-Transformationen verwenden?](https://msdn.microsoft.com/library/ee942158.aspx#web_deploy_parameters) (MSDN).
- [Mithilfe von XDT (XML Document Transformation) veröffentlicht, auf codeplex.com](https://blogs.msdn.com/b/webdev/archive/2013/04/23/xdt-xml-document-transform-released-on-codeplex-com.aspx) (.NET-Webentwicklung und Tools-Blog). Kündigt die Verfügbarkeit des Quellcodes für die Datei "Web.config" Datei-Transformations-Engine und führt einige Tools, die sie verwenden.
- [Windows Azure-Websites: Wie Zeichenfolgen und Verbindungszeichenfolgen](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (Microsoft Azure-Blog). Eine Alternative zur Datei "Web.config" transformiert werden, wenn es sich bei Ihrer zielumgebung ist Windows Azure-Websites, und Sie transformieren möchten `appSettings` oder `connectionStrings`.


<a id="webdeployparms"></a>


## <a name="using-web-deploy-parameters-to-change-settings-in-the-destination-web-application-during-deployment"></a>Verwenden von Web Deploy Parametern zum Ändern der Einstellungen in der Ziel-Webanwendung während der Bereitstellung

- [Vorgehensweise: Verwenden von Web Deploy-Parametern in einem Webbereitstellungspaket](https://msdn.microsoft.com/library/ff398068.aspx) (MSDN).
- [MSDeploy: Vorgehensweise beim Aktualisieren der app-Einstellungen für das Veröffentlichen auf Grundlage des Veröffentlichungsprofils](http://sedodream.com/2013/03/02/MSDeployHowToUpdateAppSettingsOnPublishBasedOnThePublishProfile.aspx) (Sayed Hashimi-Blog). Zeigt, wie Sie integrieren Web deploy-Parametern in Visual Studio Profile veröffentlichen.
- [Web-bereitstellen-Parametrisierung](https://www.iis.net/learn/publish/using-web-deploy/web-deploy-parameterization) (IIS.NET-Website).
- [Web-bereitstellen-Parametrisierung in Aktion](http://vishaljoshi.blogspot.com/2010/07/web-deploy-parameterization-in-action.html) (Vishal Joshi Blog).
- [Visual Studio Web-Parametrisierung bereitstellen. Web.config-Transformation](http://vishaljoshi.blogspot.com/2010/06/parameterization-vs-webconfig.html) (Vishal Joshi Blog).
- [Windows Azure-Websites: Wie Zeichenfolgen und Verbindungszeichenfolgen](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (Microsoft Azure-Blog). Eine Alternative zu Web deploy-Parametern Ihrer zielumgebung ist Windows Azure-Websites, und Sie parametrisieren möchten `appSettings` oder `connectionStrings`.


<a id="appoffline"></a>


## <a name="making-sure-an-application-is-off-line-during-deployment"></a>Sicherstellen einer Anwendungs ist offline, während der Bereitstellung

- [ASP.NET-webbereitstellung mithilfe von Visual Studio: Bereitstellen eines Codeupdates](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md). Finden Sie im Abschnitt **nehmen Sie die Anwendung offline schalten, während der Bereitstellung.**
- [Offlineschalten einer Anwendung vor der Veröffentlichung](https://www.iis.net/learn/publish/deploying-application-packages/taking-an-application-offline-before-publishing) (IIS.NET-Website). Erläutert, eine Funktion, die in Web Deploy 3.0, die Verarbeitung von einer app automatisiert integriert\_offline.htm-Datei. Dieses Feature funktioniert nicht mit benutzerdefinierter app\_offline.htm-Dateien.
- [Wie Sie Ihre Web-app während der Veröffentlichung](http://sedodream.com/2012/01/08/HowToTakeYourWebAppOfflineDuringPublishing.aspx) (Sayed Hashimi-Blog). Gewusst wie: Verwenden einer benutzerdefinierten Anwendung automatisieren\_offline.htm-Datei.
- [Veröffentlichen von Updates für die app offline und Usechecksum Web](https://blogs.msdn.com/b/webdev/archive/2013/10/30/web-publishing-updates-for-app-offline-and-usechecksum.aspx) (Blog zur Microsoft-Web-Entwicklung). Eine weitere Option für die Verwendung der app Automatisierung\_offline.htm-Datei.
- [Web bereitstellen 3.5 RTW](https://blogs.iis.net/msdeploy/archive/2013/07/09/webdeploy-3-5-rtw.aspx) (IIS.NET-Website). Neues Feature im Web bereitstellen 3.5 für benutzerdefinierte app\_offline.htm-Dateien.


<a id="databasewithweb"></a>


## <a name="deploying-a-database-or-changes-to-a-database-as-part-of-web-application-deployment"></a>Bereitstellen einer Datenbank oder Änderungen in einer Datenbank als Teil der Bereitstellung von Webanwendungen

- [Konfigurieren die Datenbankbereitstellung in Visual Studio](https://msdn.microsoft.com/library/dd394698.aspx#dbdeployment) (MSDN). Überblick über die Optionen zum Bereitstellen einer Datenbank mit einem Webprojekt.
- [ASP.NET-webbereitstellung mithilfe von Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). 12-Teil der tutorialreihe, zeigt die datenbankbereitstellung mit DbDacFx-Anbieter und Entity Framework Code First-Migrationen.
- [Vorgehensweise: Bereitstellen einer Web-Projekt mithilfe der One-Click-Veröffentlichung in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (MSDN).
- [Bereitstellen eine sicheren ASP.NET MVC 5-app mit Mitgliedschaft, OAuth und SQL-Datenbank auf einer Windows Azure-Web-Website](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Ein langes Lernprogramm, das Erstellen und Bereitstellen eine Anwendung mit einem einzelnen SQL Server-Datenbank für Mitgliedschafts-und Anwendungsdaten.
- [Bereitstellen einer ASP.NET-Webanwendung mit SQL Server Compact mit Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). 12-Teil der tutorialreihe, zeigt, wie Sie SQL Server Compact-Datenbanken bereitstellen und Migrieren von SQL Server Compact auf eine Vollversion von SQL Server.

Finden Sie in Webanwendung auch erstellen und einem Webbereitstellungspaket installieren und Bereitstellen einer Webanwendung unter Verwendung eines continuous Integration (CI)-Prozesses zuvor auf dieser Seite bereitstellen.


<a id="databaseseparate"></a>


## <a name="deploying-a-database-separately-from-web-application-deployment"></a>Bereitstellen einer Datenbank getrennt von der Bereitstellung von Webanwendungen

- [SQL Server-Datentools](https://msdn.microsoft.com/library/hh272686(v=vs.103).aspx) (MSDN).
- [Einschließlich der Daten in eine SQL Server-Datenbankprojekt](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx) (SQL Server Data Tools team-Blog). Informationen zum Schema und Daten bereitstellen, wenn Sie eine Datenbank bereitstellen.
- [Gewusst wie: Bereitstellen einer Datenbank in Windows Azure](https://docs.microsoft.com/azure/sql-database/sql-database-cloud-migrate) (Microsoft Azure-Website)
- [Migrieren von Datenbanken zu Windows Azure SQL-Datenbank (früher SQL Azure)](https://msdn.microsoft.com/library/windowsazure/ee730904.aspx) (MSDN).
- [Migrieren einer Datenbank zu SQL Azure mithilfe von SSDT](https://blogs.msdn.com/b/ssdt/archive/2012/04/19/migrating-a-database-to-sql-azure-using-ssdt.aspx) (SQL Server Data Tools team-Blog).
- [Migrieren von datenorientierten Anwendungen zu Windows Azure](https://msdn.microsoft.com/library/jj156154.aspx) (MSDN).
- [Migrieren von SQL Server-Datenbanken zu Windows Azure SQL-Datenbank](https://msdn.microsoft.com/library/windowsazure/jj156160.aspx) (MSDN).


<a id="aspnetmembership"></a>


## <a name="deploying-a-web-application-that-uses-aspnet-application-services-such-as-membership-and-profiling"></a>Bereitstellen einer Webanwendung, die ASP.NET-Anwendung verwendet Dienste, z.B. die Mitgliedschaft und profilerstellung

- [Bereitstellen eine sicheren ASP.NET MVC 5-app mit Mitgliedschaft, OAuth und SQL-Datenbank auf einer Windows Azure-Web-Website](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Ein langes Lernprogramm, das Erstellen und Bereitstellen eine Anwendung mit einem einzelnen SQL Server-Datenbank für Mitgliedschafts-und Anwendungsdaten.
- [ASP.NET Identity](https://asp.net/identity/). Ressourcen für ASP.NET Identity.
- [ASP.NET-webbereitstellung mithilfe von Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). Teil 12 tutorialreihe wird gezeigt, wie eine ASP.NET-Mitgliedschaftsdatenbank bereitgestellt wird.
- [Konfigurieren einer Website, die Anwendungsdiensten](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-a-website-that-uses-application-services-cs.md). Für die Website Projekte jedoch gilt auch für Webanwendungsprojekte.
- [Benutzer und Rollen auf der Produktionswebsite](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/users-and-roles-on-the-production-website-cs.md). Für die Website Projekte jedoch gilt auch für Webanwendungsprojekte.


<a id="precompiling"></a>


## <a name="precompiling-for-deployment"></a>Vorkompilieren für die Bereitstellung

- [ASP.NET Web Application Project Übersicht über die Vorkompilierung](https://msdn.microsoft.com/library/aa983464.aspx) (MSDN).
- [Packen/veröffentlichen-Registerkarte "Web", Projekteigenschaften](https://msdn.microsoft.com/library/dd410108.aspx) (MSDN).
- [Erweiterte Einstellungen (Dialogfeld) Vorkompilieren](https://msdn.microsoft.com/library/hh475319.aspx) (MSDN).


<a id="intranet"></a>


## <a name="deploying-an-intranet-web-application"></a>Bereitstellen einer Intranet-Web-Anwendung

- [Verwenden Sie die lokalen Organisationsauthentifizierung-Option (ADFS) mit ASP.NET in Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/) (Blog von Vittorio Bertocci.).
- [Gewusst wie: Erstellen einer Intranetsite mit ASP.NET MVC](https://msdn.microsoft.com/library/gg703322(VS.98).aspx) (MSDN). Ältere Exemplarische Vorgehensweise geschrieben für Visual Studio 2010 wird nicht angezeigt, größeren Änderungen bei den Intranet-Projektvorlagen in Visual Studio 2013 eingeführt wurden.


<a id="automating"></a>


## <a name="automating-common-deployment-tasks-that-are-not-automated-out-of-the-box"></a>Automatisieren häufige Bereitstellungsaufgaben, die standardmäßig nicht automatisiert werden

- [ASP.NET-webbereitstellung mithilfe von Visual Studio: Bereitstellen von zusätzlichen Dateien](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md).
- [Festlegen von Berechtigungen für Ordner für die Webveröffentlichung](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) (Sayed Hashimi-Blog).
- [Erweitern der Targets-Datei zur Einbeziehung von registrierungseinstellungen für eine Web-Projektpaket](https://blogs.msdn.com/webdevtools/archive/2010/02/09/how-to-extend-target-file-to-include-registry-settings-for-web-project-package.aspx) (Webentwicklungstools-Blog).
- [Erweitern die Transformation von XML (Web.config)](http://sedodream.com/2010/09/09/ExtendingXMLWebconfigConfigTransformation.aspx) (Sayed Hashimi-Blog). Zeigt, wie zum Erstellen benutzerdefinierter XDT-Transformationen.
- [Web Deployment Tool (MSDeploy) benutzerdefinierte Anbieter Take 1](http://sedodream.com/2010/03/11/WebDeploymentToolMSDeployCustomProviderTake1.aspx) (Sayed Hashimi-Blog). Zeigt, wie Sie einen benutzerdefinierten Web Deploy-Anbieter erstellen.
- [Das Verpacken und Bereitstellen von COM-Komponenten](https://blogs.msdn.com/webdevtools/archive/2010/03/03/how-to-package-and-deploy-com-component.aspx) (Webentwicklungstools-Blog).
- [Wie Sie .NET paketassemblys](https://blogs.msdn.com/webdevtools/archive/2010/02/19/how-to-package-com-component.aspx) (Webentwicklungstools-Blog). Informationen zum Bereitstellen von Assemblys im globalen Assemblycache.
- [Skript für alles – initialisieren Ihrer Windows Azure-VM für Ihre Web-Server mit IIS, Web Deploy- und andere Dinge](http://www.tugberkugurlu.com/archive/script-out-everything-initialize-your-windows-azure-vm-for-your-web-server-with-iis-web-deploy-and-other-stuff) (Tugberk Ugurlu Blog).


<a id="configuringservers"></a>


## <a name="configuring-web-servers-so-that-developers-can-deploy-web-applications-to-them-using-web-deploy"></a>Konfigurieren von Webservern, damit Entwickler Webanwendungen, die Sie mit der Web Deploy bereitstellen können

- [Installieren und Konfigurieren von Web Deploy für Administrator- und nicht-Administrator Bereitstellungen](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy) (IIS.NET-Website).


<a id="hostingprovider"></a>


## <a name="configuring-servers-for-a-hosting-provider"></a>Konfigurieren von Servern für einen Hostinganbieter

- [Bereitstellungshandbuch für Microsoft ASP.NET 4-Hosting](https://go.microsoft.com/fwlink/?LinkId=191365) (Microsoft Download Center).
- [Generieren einer XML-Datei](https://www.iis.net/learn/web-hosting/joining-the-web-hosting-gallery/generate-a-profile-xml-file) (IIS.NET-Website).


<a id="troubleshooting"></a>


## <a name="troubleshooting-deployment-problems"></a>Behandlung von Bereitstellungsproblemen

- [Problembehandlung bei Windows Azure-Websites in Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio) (Microsoft Azure-Website).
- [ASP.NET-webbereitstellung mithilfe von Visual Studio: Problembehandlung bei](../web-forms/overview/deployment/visual-studio-web-deployment/troubleshooting.md).
- [Problembehandlung bei allgemeine Problemen mit Web bereitstellen](https://www.iis.net/learn/publish/troubleshooting-web-deploy/troubleshooting-common-problems-with-web-deploy).
- [Web-bereitstellen-Fehlercodes](https://www.iis.net/learn/publish/troubleshooting-web-deploy/web-deploy-error-codes) (IIS.NET-Website).
- [Bereitstellung – häufig gestellte Fragen für Visual Studio und ASP.NET Web](https://msdn.microsoft.com/library/ee942158.aspx) (MSDN).
- [Unterschiede zwischen IIS und ASP.NET Development Server Core](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md).
- [Allgemeine Konfigurationsunterschiede zwischen Entwicklungs- und Produktionsumgebungen](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/common-configuration-differences-between-development-and-production-cs.md).
- [Hosten von ASP.NET-Anwendungen auf der mittleren Vertrauensebene](http://www.4guysfromrolla.com/articles/100307-1.aspx) (4 Guys von Rolla-Website).


<a id="gettinghelp"></a>


## <a name="getting-help-with-a-specific-deployment-question"></a>Abrufen von Hilfe mit einer bestimmten Bereitstellung Frage

- [ASP.NET-Konfiguration und Bereitstellung Forum](https://forums.asp.net/26.aspx/1?Configuration and Deployment).
- [StackOverflow.com](http://www.StackOverflow.com).


<a id="additional"></a>


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Dieser Abschnitt enthält Links zu zusätzlichen Ressourcen, die Weitere Informationen zum Verwenden von Visual Studio und IIS-Bereitstellungstools nützlich sind.

Den folgenden Blogs enthalten häufig Informationen zur Bereitstellung von Visual Studio:

- [Webentwicklungstools at Microsoft-Blog](https://blogs.msdn.com/b/webdevtools/).
- [Blog von sayed Hashimi](http://www.sedodream.com/).

Die folgenden Ressourcen bieten Dokumentation zu Web Deploy, das IIS-Framework, das Visual Studio verwendet wird, um die Web Application Project-Bereitstellungsaufgaben auszuführen. Sie können Fragen zu Web Deploy, in der [Web Deployment Tool Forum](https://go.microsoft.com/fwlink/?LinkId=149411) auf der Website IIS.net.

- [Einführung in Web Deploy](https://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy).
- [Installieren und Konfigurieren von Web bereitstellen](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy).
- [PowerShell-Skripts zum Automatisieren von Web Setup bereitstellen](https://www.iis.net/learn/publish/using-web-deploy/powershell-scripts-for-automating-web-deploy-setup).
- [Webbereitstellungstool](https://go.microsoft.com/fwlink/?LinkId=151481). Tabelle der obersten Ebene Inhaltsverzeichnisknoten für Web Deploy-Dokumentation auf der TechNet-Website. Enthält Referenzinformationen nützlich, aber die meisten der TechNet-Seiten nicht jahrelang aktualisiert wurden.
- ["Microsoft.Web.Deployment"-Namespace](https://go.microsoft.com/fwlink/?LinkId=148630). API-Dokumentation wurde nicht seit Version 1.0 aktualisiert wurde.
- [Die Microsoft Web Deployment-Teamblog](https://blogs.iis.net/msdeploy/default.aspx).
- [Registerkarte veröffentlichen, in der Website IIS.net](https://www.iis.net/learn/publish).
