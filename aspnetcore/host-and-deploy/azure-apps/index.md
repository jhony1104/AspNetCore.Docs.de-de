---
title: Bereitstellen von ASP.NET Core-Apps in Azure App Service
author: bradygaster
description: Dieser Artikel enthält Links zu Azure-Host- und Bereitstellungsressourcen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/11/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 392868b4fc9105279f8f3b10436a9915123e7070
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190660"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Bereitstellen von ASP.NET Core-Apps in Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core.

## <a name="useful-resources"></a>Nützliche Ressourcen

In der [App Service-Dokumentation](/azure/app-service/) finden Sie die Azure-Apps-Dokumentation, Tutorials, Beispiele und Leitfäden sowie andere Ressourcen. Dies sind zwei erwähnenswerte Tutorials, die auf das Hosten von ASP.NET Core-Apps eingehen:

[Erstellen einer ASP.NET Core-Web-App in Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Verwenden Sie Visual Studio, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Windows bereitzustellen.

[Erstellen einer .NET Core-App in App Service für Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Verwenden Sie die Befehlszeile, um ASP.NET Core-Web-Apps zu erstellen und in Azure App Service unter Linux bereitzustellen.

Die ASP.NET Core-Version, die unter Azure App Service verfügbar ist, finden Sie unter [ASP.NET Core im App Service-Dashboard](https://aspnetcoreon.azurewebsites.net/).

Abonnieren Sie das Repository für [App Service-Ankündigungen](https://github.com/Azure/app-service-announcements/), und überwachen Sie die Issues. Das App Service-Team postet regelmäßig Ankündigungen und neue Szenarien in App Service.

Die folgenden Artikel sind in der ASP.NET Core-Dokumentation verfügbar:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.

[Erstellen Sie Ihre erste Pipeline](/azure/devops/pipelines/get-started-yaml)  
Richten Sie ein CI-Build für eine ASP.NET Core-App ein, und erstellen Sie dann ein Continuous Deployment-Release für Azure App Service.

[Azure Web App-Sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Entdecken Sie die Einschränkungen der Azure App Service-Laufzeitausführung, die durch die Azure Apps-Plattform erzwungen werden.

<xref:test/troubleshoot>  
In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.

## <a name="application-configuration"></a>Anwendungskonfiguration

### <a name="platform"></a>Plattform

::: moniker range=">= aspnetcore-2.2"

In Azure App Service sind Runtimes für 64-Bit- und 32-Bit-Apps (x64 und x86) vorhanden. Das [.NET Core SDK](/dotnet/core/sdk), das in Azure App Service zur Verfügung steht, ist eine 32-Bit-Version. Sie können jedoch auch lokal erstellte 64-Bit-Apps mit der [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole oder dem Veröffentlichungsprozess in Visual Studio bereitstellen. Weitere Informationen finden Sie im Abschnitt [Publish and deploy the app (Veröffentlichen und Bereitstellen der App)](#publish-and-deploy-the-app).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Für Apps mit nativen Abhängigkeiten stehen in Azure App Service Runtimes für 32-Bit- und 32-Bit-Apps (x86 und x86) zur Verfügung. Das [.NET Core SDK](/dotnet/core/sdk) in Azure App Service ist eine 32-Bit-Version.

::: moniker-end

Weitere Informationen zu .NET Core-Frameworkkomponenten und Verteilungsmethoden (z. B. .NET Core-Runtime und .NET Core SDK) finden Sie unter [Weitere Informationen zu .NET Core: Aufbau](/dotnet/core/about#composition).

### <a name="packages"></a>Pakete

Beziehen Sie die folgenden NuGet-Pakete ein, die automatische Protokollierungsfeatures für Apps bieten, die für Azure App Service bereitgestellt werden:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) verwendet [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration), um die ASP.NET Core-Lightup-Integration mit Azure App Service bereitzustellen. Die hinzugefügten Protokollierungsfeatures werden vom `Microsoft.AspNetCore.AzureAppServicesIntegration`-Paket bereitgestellt.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) führt [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) aus, um Anbieter für die Azure App Service-Diagnoseprotokollierung zum Paket `Microsoft.Extensions.Logging.AzureAppServices` hinzuzufügen.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) stellt Protokollierungsimplementierungen bereit, um die Azure App Service-Features für Diagnoseprotokolle und Protokollstreaming zu unterstützen.

Die Metapakete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) sind nicht in den vorherigen Paketen enthalten. Apps, deren Ziel das .NET Framework ist oder die auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, müssen explizit auf die einzelnen Pakete in der Projektdatei der App verweisen.

## <a name="override-app-configuration-using-the-azure-portal"></a>Überschreiben der App-Konfiguration im Azure-Portal

Über die App-Einstellungen im Azure-Portal können Sie Umgebungsvariablen für die App festlegen. Umgebungsvariablen können von [Umgebungsvariablen-Konfigurationsanbietern](xref:fundamentals/configuration/index#environment-variables-configuration-provider) verarbeitet werden.

Wenn eine App-Einstellung im Azure-Portal erstellt oder geändert und die Schaltfläche **Speichern** ausgewählt wird, wird die Azure-App neu gestartet. Die Umgebungsvariable steht der App nach dem Neustart des Diensts zur Verfügung.

::: moniker range=">= aspnetcore-3.0"

Wenn eine App den [generischen Host](xref:fundamentals/host/generic-host) verwendet, werden Umgebungsvariablen nicht standardmäßig in die App-Konfiguration geladen, und der Konfigurationsanbieter muss vom Entwickler hinzugefügt werden. Der Entwickler legt das Präfix der Umgebungsvariablen fest, wenn der Konfigurationsanbieter hinzugefügt wird. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Wenn eine App den Host mit [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) erstellt, verwenden Umgebungsvariablen, die den Host konfigurieren, das Präfix `ASPNETCORE_`. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host> und unter [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Die Middleware für die [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), die beim [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Hosting die Middleware für weitergeleitete Header konfiguriert, und das ASP.NET Core-Modul sind so konfiguriert, dass sie das Schema (HTTP/HTTPS) und die Remote-IP-Adresse an die Stelle weiterleiten, von der die Anforderung stammte. Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

::: moniker range=">= aspnetcore-3.0"

In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core Logging Integration** (ASP.NET Core-Protokollierungsintegration). Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In App Service bereitgestellte ASP.NET Core-Apps erhalten automatisch die App Service-Erweiterung **ASP.NET Core-Protokollierungserweiterungen**. Durch diese Erweiterung ist die Protokollierungsintegration für ASP.NET Core-Apps in Azure App Service möglich.

::: moniker-end

In den folgenden Artikeln finden Sie Informationen zum Überwachen, Protokollieren und zur Problembehandlung:

[Überwachen von Apps in Azure App Service](/azure/app-service/web-sites-monitor)  
Erfahren Sie, wie Sie Kontingente und Metrik für Apps und App Service-Pläne prüfen.

[Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Erfahren Sie, wie Sie die Diagnosesprotokollierung für HTTP-Statuscodes, fehlgeschlagene Anforderungen und Webserveraktivitäten aktivieren und auf die Protokolle zugreifen.

<xref:fundamentals/error-handling>  
Lernen Sie gängige Methoden zur Fehlerbehandlung in ASP.NET Core-Apps kennen.

<xref:test/troubleshoot-azure-iis>  
Erfahren Sie, wie Sie Probleme mit Azure App Service-Bereitstellungen mit ASP.NET Core-Apps diagnostizieren können.

<xref:host-and-deploy/azure-iis-errors-reference>  
Sehen Sie sich häufig auftretende Fehler und entsprechende Hinweise zur Fehlerbehebung bei der Bereitstellungskonfiguration für von Azure App Service/IIS gehosteten Apps an.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Data Protection-Schlüsselbund und -Bereitstellungsslots

[Data Protection-Schlüssel](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) werden im Ordner *%HOME%\ASP.NET\DataProtection-Keys* gespeichert. Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert. Ruhende Schlüssel werden nicht geschützt. Dieser Ordner stellt den Schlüsselbund für alle Instanzen einer App in einem einzelnen Bereitstellungsslot bereit. Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund.

Wenn Sie zwischen Bereitstellungsslots wechseln, können Systeme, die Data Protection verwenden, gespeicherte Daten nicht mit dem Schlüsselbund des vorherigen Slots entschlüsseln. ASP.NET-Cookiemiddleware verwendet Data Protection zum Schutz ihrer Cookies. Dies führt dazu, dass Benutzer in einer App abgemeldet werden, die Standard-ASP.NET-Cookiemiddleware verwendet. Verwende Sie einen externen Schlüsselbundanbieter für eine slotunabhängige Schlüsselbundlösung wie z.B.:

* Azure Blob Storage
* Azure Key Vault
* SQL-Speicher
* Redis Cache

Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-aspnet-core-30-to-azure-app-service"></a>Bereitstellen von ASP.NET Core 3.0 in Azure App Service

ASP.NET Core 3.0 wird in Azure App Service unterstützt. Um eine Vorschauversion einer höheren .NET Core-Version als 3.0 bereitzustellen, verwenden Sie eins der folgenden Verfahren. Diese Verfahren werden auch verwendet, wenn die Runtime verfügbar ist, aber das SDK nicht in Azure App Service installiert wurde.

* [Angeben der .NET Core SDK-Version mithilfe von Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Bereitstellen einer eigenständigen Vorschau-App](#deploy-a-self-contained-preview-app)
* [Verwenden von Docker mit Web-Apps für Container](#use-docker-with-web-apps-for-containers).
* [Installieren der Websiteerweiterung (Vorschau)](#install-the-preview-site-extension).

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Angeben der .NET Core SDK-Version mithilfe von Azure Pipelines

Verwenden Sie die Szenarien zum [Entwerfen einer CI/CD-Pipeline mithilfe von Azure DevOps](/azure/app-service/deploy-continuous-deployment), um einen Continuous Integration-Build mit Azure DevOps einzurichten. Nach dem Erstellen des Azure DevOps-Builds können Sie diesen optional für die Verwendung einer bestimmten SDK-Version konfigurieren. 

#### <a name="specify-the-net-core-sdk-version"></a>Angeben der .NET Core SDK-Version

Wenn Sie das App Service-Bereitstellungscenter zum Erstellen eines Azure DevOps-Builds verwenden, enthält die Standardbuildpipeline die Schritte für `Restore`, `Build`, `Test` und `Publish`. Zum Angeben der SDK-Version klicken Sie auf die Schaltfläche **Hinzufügen (+)** in der Liste mit Agent-Aufträgen, um einen neuen Schritt hinzuzufügen. Suchen Sie in der Suchleiste nach **.NET Core SDK**. 

![Hinzufügen des .NET Core SDK-Schritts](index/add-sdk-step.png)

Verschieben Sie den Schritt an die erste Stelle im Build, sodass die nachfolgenden Schritte die angegebene Version des .NET Core SDK verwenden. Geben Sie die Version des .NET Core SDK an. In diesem Beispiel ist das SDK auf `3.0.100` festgelegt.

![Abgeschlossener SDK-Schritt](index/sdk-step-first-place.png)

Um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) (Self-Contained Deployment, SCD) zu veröffentlichen, konfigurieren Sie SCD im Schritt `Publish`, und geben Sie den [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) an.

![Veröffentlichung einer eigenständigen Bereitstellung](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Bereitstellen einer eigenständigen Vorschau-App

Eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd), die auf eine Vorschauruntime abzielt, enthält die Vorschauruntime in der Bereitstellung.

Wenn Sie eine eigenständige App bereitstellen, gilt Folgendes:

* Die Site in Azure App Service erfordert nicht die [Vorschau-Websiteerweiterung](#install-the-preview-site-extension).
* Die App muss gemäß einem anderen Ansatz veröffentlicht werden, als beim Veröffentlichen für eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Befolgen Sie die Anweisungen im Abschnitt [Bereitstellen einer eigenständigen Vorschau-App](#deploy-the-app-self-contained).

### <a name="use-docker-with-web-apps-for-containers"></a>Verwenden von Docker mit Web-Apps für Container

Der [Docker-Hub](https://hub.docker.com/r/microsoft/aspnetcore/) enthält die aktuellen Images für die Docker-Vorschauversion. Die Images können als Basisimage verwendet werden. Verwenden Sie das Image, und führen Sie wie gewohnt eine Bereitstellung für Web-Apps für Container durch.

### <a name="install-the-preview-site-extension"></a>Installieren der Websiteerweiterung (Vorschau)

Sollte ein Problem mit dem Verwenden der Vorschau der Websiteerweiterung auftreten, öffnen Sie ein [aspnet/AspNetCore-Issue](https://github.com/aspnet/AspNetCore/issues).

1. Navigieren Sie im Azure-Portal zu „App Service“.
1. Wählen Sie die Web-App aus.
1. Geben Sie „ex“ in das Suchfeld ein, um nach „Extensions“ (Erweiterungen) zu filtern, oder durchsuchen Sie die Liste der Verwaltungstools.
1. Wählen Sie **Erweiterungen** aus.
1. Wählen Sie **Hinzufügen** aus.
1. Wählen Sie die Erweiterung **ASP.NET Core {X.Y} ({x64|x86}) Runtime** aus der Liste aus. Dabei ist `{X.Y}` die ASP.NET Core-Vorschauversion, und `{x64|x86}` gibt die Plattform an.
1. Klicken Sie auf **OK**, um die rechtlichen Bedingungen zu akzeptieren.
1. Wählen Sie **OK** aus, um die Erweiterung zu installieren.

Nach Abschluss dieses Vorgangs wird die neueste .NET Core-Vorschauversion installiert. Überprüfen Sie die Installation:

1. Wählen Sie **Erweiterte Tools** aus.
1. Wählen Sie **Go** unter **Erweiterte Tools** aus.
1. Wählen Sie das Menüelement **Debugkonsole** > **PowerShell** aus.
1. Führen Sie in der PowerShell-Eingabeaufforderung den folgenden Befehl aus. Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion durch `{X.Y}` und die Plattform durch `{PLATFORM}`:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.

> [!NOTE]
> Die Plattformarchitektur (x86/x64) einer App Services-App wird in den Einstellungen der App im Azure-Portal festgelegt. Dies gilt für Apps, die in einem A-Series-Computetarif oder einem höheren Hostingtarif gehostet werden. Wenn die Anwendung im In-Process-Modus ausgeführt wird und die Plattformarchitektur für 64-Bit (x64) konfiguriert ist, verwendet das ASP.NET Core-Modul die 64-Bit-Vorschauruntime, falls vorhanden. Installieren Sie die Erweiterung **ASP.NET Core {X.Y} (x64) Runtime**.
>
> Nach der Installation der x64-Vorschauruntime führen Sie den folgenden Befehl im Kudu PowerShell-Befehlsfenster aus, um die Installation zu überprüfen. Ersetzen Sie im Befehl die ASP.NET Core-Runtimeversion für `{X.Y}`:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> Der Befehl gibt `True` zurück, wenn die x64-Vorschauruntime installiert ist.

> [!NOTE]
> **ASP.NET Core-Erweiterungen** aktivieren zusätzliche Funktionen für ASP.NET Core in Azure App Services, z.B. Azure-Protokollierung. Die Erweiterung wird automatisch installiert, wenn die Bereitstellung aus Visual Studio erfolgt. Wenn die Erweiterung nicht installiert ist, installieren Sie sie für die App.

**Verwenden der Vorschau-Websiteerweiterung mit einer ARM-Vorlage**

Wenn Sie eine ARM-Vorlage zum Erstellen und Bereitstellen von Anwendungen verwenden, können Sie den Ressourcentyp `siteextensions` verwenden, um die Websiteerweiterung zu einer Web-App hinzuzufügen. Beispiel:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Veröffentlichen und Bereitstellen der App

### <a name="deploy-the-app-framework-dependent"></a>Frameworkabhängige Bereitstellung einer App

::: moniker range=">= aspnetcore-2.2"

Von [64-Bit-Frameworks abhängige Bereitstellungen](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

* Verwenden Sie ein .NET Core SDK mit 64 Bit, um eine 64-Bit-App zu erstellen.
* Legen Sie die **Plattform** in App Service unter **Konfiguration** > **Allgemeine Einstellungen** auf **64 Bit** fest. Die App muss den Serviceplan „Basic“ oder höher verwenden, damit die Bitanzahl der Plattform festgelegt werden kann.

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.
1. Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.
1. Wählen Sie **Erweitert** aus. Das Dialogfeld **Veröffentlichen** wird geöffnet.
1. Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:
   * Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.
   * Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Frameworkabhängig** aus.
   * Wählen Sie **Portierbar** für **Zielruntime** aus.
   * Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.
   * Klicken Sie auf **Speichern**.
1. Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Geben Sie in der Projektdatei keine [RID (relativer Bezeichner)](/dotnet/core/rid-catalog) an.

1. Nutzen Sie die Befehlsshell, um die App mit der Konfiguration „Release“ mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) zu veröffentlichen. Im folgenden Beispiel wird die App als frameworkabhängige App veröffentlicht:

   ```console
   dotnet publish --configuration Release
   ```

1. Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/publish* in den Speicherort in App Service. Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die [Kudu](https://github.com/projectkudu/kudu/wiki)-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.

---

### <a name="deploy-the-app-self-contained"></a>Bereitstellen der App als eigenständige App

Nutzen Sie für [eigenständige Bereitstellungen](/dotnet/core/deploying/#self-contained-deployments-scd) Visual Studio oder die CLI-Tools.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie in der Visual Studio-Symbolleiste auf **Erstellen** >  **{Anwendungsname} veröffentlichen**, oder klicken Sie mit der rechten Maustaste auf den **Projektmappen-Explorer**, und klicken Sie dann auf **Veröffentlichen**.
1. Vergewissern Sie sich im Dialogfeld **Veröffentlichungsziel auswählen**, dass **App Service** ausgewählt ist.
1. Wählen Sie **Erweitert** aus. Das Dialogfeld **Veröffentlichen** wird geöffnet.
1. Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:
   * Überprüfen Sie, ob die Konfiguration **Release** ausgewählt ist.
   * Öffnen Sie die Dropdownliste **Bereitstellungsmodus**, und wählen Sie **Eigenständig** aus.
   * Wählen Sie die Zielruntime in der Dropdownliste **Zielruntime** aus. Die Standardeinstellung ist `win-x86`.
   * Wenn Sie zusätzliche Dateien bei der Bereitstellung entfernen müssen, öffnen Sie **Dateiveröffentlichungsoptionen**, und aktivieren Sie das Kontrollkästchen, um zusätzliche Dateien am Ziel zu entfernen.
   * Klicken Sie auf **Speichern**.
1. Sie können eine neue Website erstellen oder eine vorhandene aktualisieren, indem Sie die folgenden Anweisungen des Veröffentlichungs-Assistenten befolgen.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Geben Sie in der Projektdatei eine oder mehrere [Runtimebezeichner (RIDs)](/dotnet/core/rid-catalog) an. Verwenden Sie die Singularform `<RuntimeIdentifier>` für eine einzelne RID oder die Pluralform `<RuntimeIdentifiers>`, um eine durch Semikolons getrennte Liste von RIDs bereitzustellen. Im folgenden Beispiel wird die RID `win-x86` angegeben:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Veröffentlichen Sie aus einer Befehlsshell die App in der Releasekonfiguration für die Runtime des Hosts mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish). Im folgenden Beispiel wird die App für die RID `win-x86` veröffentlicht. Die für die Option `--runtime` angegebene RID muss in der Eigenschaft `<RuntimeIdentifier>` (oder `<RuntimeIdentifiers>`) in der Projektdatei angegeben werden.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Verschieben Sie den Inhalt des Verzeichnisses *bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* auf die Site in App Service. Wenn Sie die Inhalte des Ordners *publish* von Ihrer lokalen Festplatte oder Netzwerkfreigabe direkt über die Kudu-Konsole in App Service verschieben möchten, ziehen Sie die Dateien in den Ordner `D:\home\site\wwwroot` in der Kudu-Konsole.

---

## <a name="protocol-settings-https"></a>Protokolleinstellungen (HTTPS)

Durch sichere Protokollbindungen können Sie ein Zertifikat festlegen, das verwendet werden soll, wenn über HTTPS auf Anforderungen geantwortet wird. Bindungen erfordern gültige private Zertifikate ( *.pfx*), die für den angegebenen Hostnamen ausgestellt wurden. Weitere Informationen finden Sie unter [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformieren von web.config

Wenn Sie *web.config* beim Veröffentlichen transformieren müssen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festlegen müssen), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [App Service-Übersicht](/azure/app-service/app-service-web-overview)
* [Azure App Service: The Best Place to Host your .NET Apps (Azure App Service: Ideal zum Hosten von .NET-Apps; 55-minütiges Video)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (Azure Friday: Azure App Service-Diagnose und -Fehlerbehebung; zwölfminütiges Video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Übersicht: Azure App Service-Diagnose](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Azure App Service auf Windows Server verwendet [Internetinformationsdienste (IIS)](https://www.iis.net/). Die folgenden Artikel gehen auf die zugrunde liegende IIS-Technologie ein:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server – IT-Administrator-Inhalt für aktuelle und frühere Versionen](/windows-server/windows-server-versions)
