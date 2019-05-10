---
title: Continuous Integration und Continuous Deployment - DevOps mit ASP.NET Core und Azure
author: CamSoper
description: Continuous Integration und Continuous Deployment in DevOps mit ASP.NET Core und Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893997"
---
# <a name="continuous-integration-and-deployment"></a>Continuous Integration und Continuous deployment

Im vorherigen Kapitel haben Sie ein lokales Git-Repository für die einfache Feed-Reader-app erstellt. In diesem Kapitel Sie diesen Code in einem GitHub-Repository veröffentlichen und eine Azure DevOps-Dienste-Pipeline mithilfe von Azure-Pipelines zu erstellen. Die Pipeline ermöglicht fortlaufende Builds und Bereitstellungen der app. Jedem Commit zum GitHub-Repository löst einen Build und Bereitstellung in der Azure Web-App-staging-Slot.

In diesem Abschnitt müssen Sie die folgenden Aufgaben ausführen:

* Veröffentlichen der app-Code auf GitHub
* Trennen Sie die lokale Git-Bereitstellung
* Erstellen einer Azure DevOps-Organisation
* Erstellen eines Teamprojekts in Azure DevOps-Dienste
* Erstellen einer Builddefinition
* Erstellen Sie eine releasepipeline
* Änderungen in GitHub committen und automatisch in Azure bereitgestellt
* Überprüfen Sie die Pipeline für die Azure-Pipelines

## <a name="publish-the-apps-code-to-github"></a>Veröffentlichen der app-Code auf GitHub

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com`.
1. Klicken Sie auf die **+** Dropdownliste in der Kopfzeile, und wählen **neues Repository**:

    ![Neue GitHub-Repository-option](media/cicd/github-new-repo.png)

1. Wählen Sie Ihr Konto in der **Besitzer** Dropdown-Liste, und geben Sie *Simple-Feed-Reader* in die **Repositoryname** Textfeld.
1. Klicken Sie auf die **-Repository erstellen** Schaltfläche.
1. Öffnen Sie die Befehlsshell von Ihrem lokalen Computer. Navigieren Sie zum Verzeichnis, in dem die *Simple-Feed-Reader* Git-Repository gespeichert ist.
1. Benennen Sie die vorhandene *Ursprung* Remoteinstanz durch, um *upstream*. Führen Sie den folgenden Befehl aus:

    ```console
    git remote rename origin upstream
    ```

1. Fügen Sie einen neuen *Ursprung* remote auf Ihre Kopie des Repositorys auf GitHub. Führen Sie den folgenden Befehl aus:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Veröffentlichen Sie Ihr lokale Git-Repository zum neu erstellten GitHub-Repository. Führen Sie den folgenden Befehl aus:

    ```console
    git push -u origin master
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com/<GitHub_username>/simple-feed-reader/`. Überprüfen Sie, dass Ihr Code im GitHub-Repository angezeigt wird.

## <a name="disconnect-local-git-deployment"></a>Trennen Sie die lokale Git-Bereitstellung

Entfernen Sie die lokale Git-Bereitstellung mit den folgenden Schritten. Azure-Pipelines (eine Azure DevOps-Dienst) sowohl ersetzt und erweitert diese Funktion aus.

1. Öffnen der [Azure-Portal](https://portal.azure.com/), und navigieren Sie zu der *staging (Mywebapp\<Unique_number\>/staging)* Web-App. Die Web-App kann schnell durch Eingabe platziert werden *staging* in das Suchfeld des Portals:

    ![Staging-Web-App-Suchbegriff](media/cicd/portal-search-box.png)

1. Klicken Sie auf **Bereitstellungscenter**. Ein neuer Bereich wird angezeigt. Klicken Sie auf **trennen** So entfernen Sie die Konfiguration der quellcodeverwaltung lokale Git, das im vorherigen Kapitel hinzugefügt wurde. Bestätigen Sie den Entfernungsvorgang zum, indem Sie auf die **Ja** Schaltfläche.
1. Navigieren Sie zu der *Mywebapp < Unique_number >* App Service. Zur Erinnerung: kann das Suchfeld des Portals verwendet werden, um die App Service schnell zu finden.
1. Klicken Sie auf **Bereitstellungscenter**. Ein neuer Bereich wird angezeigt. Klicken Sie auf **trennen** So entfernen Sie die Konfiguration der quellcodeverwaltung lokale Git, das im vorherigen Kapitel hinzugefügt wurde. Bestätigen Sie den Entfernungsvorgang zum, indem Sie auf die **Ja** Schaltfläche.

## <a name="create-an-azure-devops-organization"></a>Erstellen einer Azure DevOps-Organisation

1. Öffnen Sie einen Browser, und navigieren Sie zu der [Seite zum Erstellen von Azure DevOps-Organisation](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Geben Sie einen eindeutigen Namen in der **wählen Sie einen einprägsamen Namen** Textfeld die URL für den Zugriff auf Ihre Organisation Azure DevOps bilden.
1. Wählen Sie die **Git** Optionsfeld, da der Code in einem GitHub-Repository gehostet wird.
1. Klicken Sie auf die Schaltfläche **Continue** (Weiter). Nach einer kurzen Wartezeit, ein Konto und ein Teamprojekt erstellen, mit dem Namen *MyFirstProject*, werden erstellt.

    ![Azure DevOps-Organisation-Erstellungsseite](media/cicd/vsts-account-creation.png)

1. Öffnen Sie die Bestätigungs-e-Mail gibt an, dass das Azure DevOps-Organisation und das Projekt zur Verwendung bereit sind. Klicken Sie auf die **starten Sie Ihr Projekt** Schaltfläche:

    ![Starten Sie die Projekt-Schaltfläche](media/cicd/vsts-start-project.png)

1. Öffnet ein Browser  *\<Account_name\>. visualstudio.com*. Klicken Sie auf die *MyFirstProject* Link zum Konfigurieren des Projekts DevOps-Pipeline.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurieren Sie die Pipeline für die Azure-Pipelines

Es gibt drei unterschiedliche Schritte ausführen. Die Schritte in den folgenden drei Abschnitte-Ergebnissen in einer operativen DevOps-Pipeline.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>GRANT Azure DevOps-Zugriff auf das GitHub-repository

1. Erweitern Sie die **oder erstellen Sie Code aus einem externen Repository** ' Accordion '. Klicken Sie auf die **erstellen Sie das Setup** Schaltfläche:

    ![Richten Sie die Schaltfläche "Build"](media/cicd/vsts-setup-build.png)

1. Wählen Sie die **GitHub** option die **wählen Sie eine Datenquelle** Abschnitt:

    ![Wählen Sie eine Datenquelle – GitHub](media/cicd/vsts-select-source.png)

1. Autorisierung ist erforderlich, damit Azure DevOps mit Ihrem GitHub-Repository zugreifen können. Geben Sie *< GitHub_username > GitHub-Verbindung* in die **Verbindungsname** Textfeld. Zum Beispiel:

    ![Name des GitHub-Verbindung](media/cicd/vsts-repo-authz.png)

1. Wenn Sie auf Ihr GitHub-Konto die zweistufige Authentifizierung aktiviert ist, muss ein persönliches Zugriffstoken. In diesem Fall, klicken Sie auf die **autorisieren mit einem persönlichen GitHub-Zugriffstoken** Link. Finden Sie unter den [offiziellen GitHub personal Access token Erstellung Anweisungen](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) um Hilfe zu erhalten. Nur die *Repository* Geltungsbereich von Berechtigungen ist erforderlich. Klicken Sie andernfalls auf die **mithilfe von OAuth autorisieren** Schaltfläche.
1. Wenn Sie aufgefordert werden, melden Sie sich mit Ihrem GitHub-Konto. Wählen Sie dann die autorisieren gewähren Zugriff auf Ihre Azure DevOps-Organisation. Bei Erfolg wird ein neuer Dienstendpunkt erstellt.
1. Klicken Sie auf die Schaltfläche mit den Auslassungspunkten neben der **Repository** Schaltfläche. Wählen Sie die *< GitHub_username > / Simple-Feed-Reader* Repository aus der Liste. Klicken Sie auf die **wählen** Schaltfläche.
1. Wählen Sie die *master* branch aus der **standardbranch für manuelle und geplante Builds** Dropdown-Liste. Klicken Sie auf die Schaltfläche **Continue** (Weiter). Die Auswahlseite für die Vorlage wird angezeigt.

### <a name="create-the-build-definition"></a>Erstellen der Builddefinition

1. Geben Sie an der Auswahlseite der Vorlage *ASP.NET Core* in das Suchfeld:

    ![ASP.NET Core-Suche auf der Seite "Template"](media/cicd/vsts-template-selection.png)

1. Die Vorlage Suchergebnisse angezeigt werden. Zeigen Sie auf die **ASP.NET Core** Vorlage, und klicken Sie auf die **übernehmen** Schaltfläche.
1. Die **Aufgaben** Registerkarte der Builddefinition angezeigt wird. Klicken Sie auf die **Trigger** Registerkarte.
1. Überprüfen Sie die **Aktivieren der continuous Integration** Feld. Unter den **Filter für Branches** Abschnitt sicher, dass die **Typ** Dropdownliste nastaven NA hodnotu *Include*. Legen Sie die **Branchspezifikation** Dropdown-Liste für *master*.

    ![Aktivieren Sie continuous integrationseinstellungen](media/cicd/vsts-enable-ci.png)

    Diese Einstellungen dazu führen, dass einen Build auslösen, wenn eine Änderung an gepusht wird die *master* Verzweigung des GitHub-Repositorys. Continuous Integration wird getestet, der [Änderungen in GitHub Committen und automatisch in Azure bereitgestellt](#commit-changes-to-github-and-automatically-deploy-to-azure) Abschnitt.

1. Klicken Sie auf die **speichern und in Warteschlange einreihen** Schaltfläche, und wählen Sie die **speichern** Option:

    ![Schaltfläche "Speichern"](media/cicd/vsts-save-build.png)

1. Das folgende modale Dialogfeld wird angezeigt:

    ![Speichern Sie die Builddefinition - modalen Dialogfeldern](media/cicd/vsts-save-modal.png)

    Verwenden Sie den Standardordner der *\\*, und klicken Sie auf die **speichern** Schaltfläche.

### <a name="create-the-release-pipeline"></a>Erstellen Sie die releasepipeline

1. Klicken Sie auf die **Versionen** Registerkarte des Teamprojekts. Klicken Sie auf die **neue Pipeline** Schaltfläche.

    ![Registerkarte "Releases" – Schaltfläche "neu-Definition"](media/cicd/vsts-new-release-definition.png)

    Die Vorlage Auswahlbereich angezeigt wird.

1. Geben Sie an der Auswahlseite der Vorlage *App Service* in das Suchfeld:

    ![Suchfeld für Release Pipeline-Vorlage](media/cicd/vsts-release-template-search.png)

1. Die Vorlage Suchergebnisse angezeigt werden. Zeigen Sie auf die **Azure App Service-Bereitstellung mit Slot** Vorlage, und klicken Sie auf die **übernehmen** Schaltfläche. Die **Pipeline** Registerkarte der releasepipeline wird angezeigt.

    ![Registerkarte "Pipeline" Release-pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. Klicken Sie auf die **hinzufügen** Schaltfläche der **Artefakte** Feld. Die **Hinzufügen eines Artefakts** Bereich angezeigt wird:

    ![Das releasepipeline - Artefakt-Bereich hinzufügen](media/cicd/vsts-release-add-artifact.png)

1. Wählen Sie die **erstellen** Kachel die **Quelltyp** Abschnitt. Dieser Typ ermöglicht die Verknüpfung der releasepipeline an der Builddefinition.
1. Wählen Sie *MyFirstProject* aus der **Projekt** Dropdownliste aus.
1. Wählen Sie den Namen der Builddefinition, *MyFirstProject – ASP.NET Core-CI*, aus der **Quelle (Builddefinition)** Dropdownliste aus.
1. Wählen Sie *neueste* aus der **Standardversion** Dropdownliste aus. Diese Option wird die von der letzten Ausführung der Builddefinition Artefakte erstellt.
1. Ersetzen Sie den Text in die **quellalias** Textbox mit *löschen*.
1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Die **Artefakte** Abschnitt Updates, um die Änderungen anzuzeigen.
1. Klicken Sie auf das Blitzsymbol um kontinuierliche Bereitstellungen zu aktivieren:

    ![Artefakte – Blitzsymbol-releasepipeline](media/cicd/vsts-artifacts-lightning-bolt.png)

    Diese Option aktiviert ist tritt auf, eine Bereitstellung jedes Mal, wenn ein neuer Build verfügbar ist.
1. Ein **Continuous Deployment-Auslöser** Bereich auf der rechten Seite angezeigt wird. Klicken Sie auf die Umschaltfläche, um das Feature zu aktivieren. Es ist nicht notwendig, aktivieren Sie die **Pull Request-Auslöser**.
1. Klicken Sie auf die **hinzufügen** -Dropdown in der **Buildbranchfilter** Abschnitt. Wählen Sie die **erstellen standardbranch der Builddefinition** Option. Dieser Filter führt dazu, dass die Version, nur für einen Build aus des GitHub-Repositorys auszulösen *master* Branch.
1. Klicken Sie auf die Schaltfläche **Speichern**. Klicken Sie auf die **OK** -Schaltfläche in der resultierenden **speichern** modales Dialogfeld.
1. Klicken Sie auf die **Umgebung 1** Feld. Ein **Umgebung** Bereich auf der rechten Seite angezeigt wird. Ändern der *Umgebung 1* Text in die **Umgebungsname** Textfeld *Produktion*.

   ![Releasepipeline – Textfeld "Umgebung"](media/cicd/vsts-environment-name-textbox.png)

1. Klicken Sie auf die **-Phase 1, 2 Aufgaben** -link in der **Produktion** Feld:

    ![Releasepipeline - Produktion Umgebung link.png](media/cicd/vsts-production-link.png)

    Die **Aufgaben** Registerkarte der Umgebung wird angezeigt.
1. Klicken Sie auf die **Bereitstellen von Azure App Service in Slot** Aufgabe. Die Einstellungen werden in einem Panel auf der rechten Seite angezeigt.
1. Wählen Sie das Azure-Abonnement verknüpft ist, mit dem App-Dienst von der **Azure-Abonnement** Dropdownliste aus. Nachdem Sie ausgewählt haben, klicken Sie auf die **autorisieren** Schaltfläche.
1. Wählen Sie *Web-App* aus der **App-Typ** Dropdownliste aus.
1. Wählen Sie *Mywebapp / < Unique_number / >* aus der **App Service-Name** Dropdownliste aus.
1. Wählen Sie *AzureTutorial* aus der **Ressourcengruppe** Dropdownliste aus.
1. Wählen Sie *staging* aus der **Slot** Dropdownliste aus.
1. Klicken Sie auf die Schaltfläche **Speichern**.
1. Zeigen Sie auf den Standardnamen des Release-Pipeline. Klicken Sie auf das Stiftsymbol, um ihn zu bearbeiten. Verwendung *MyFirstProject – ASP.NET Core-CD* als Namen.

    ![Name der Release-pipeline](media/cicd/vsts-release-definition-name.png)

1. Klicken Sie auf die Schaltfläche **Speichern**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Änderungen in GitHub committen und automatisch in Azure bereitgestellt

1. Open *SimpleFeedReader.sln* in Visual Studio.
1. Öffnen Sie im Projektmappen-Explorer *Pages\Index.cshtml*. Änderung `<h2>Simple Feed Reader - V3</h2>` zu `<h2>Simple Feed Reader - V4</h2>`.
1. Drücken Sie **STRG**+**UMSCHALT**+**B** zum Erstellen der app.
1. Committen Sie die Datei in das GitHub-Repository. Verwenden Sie entweder die **Änderungen** Seite in Visual Studio *Team Explorer* Registerkarte, oder führen Sie den folgenden mithilfe des lokalen Computers Befehlsshell:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Übertragen Sie die Änderung der *master* branch der *Ursprung* remote von Ihrem GitHub-Repository:

    ```console
    git push origin master
    ```

    Der Commit wird angezeigt, in des GitHub-Repositorys *master* Branch:

    ![GitHub-Commit im masterbranch](media/cicd/github-commit.png)

    Der Build ausgelöst wird, da die fortlaufende Integration aktiviert ist, in der Builddefinition **Trigger** Registerkarte:

    ![Aktivieren der continuous integration](media/cicd/enable-ci.png)

1. Navigieren Sie zu der **in der Warteschlange** Registerkarte die **Azure Pipelines** > **erstellt** Seite in Azure DevOps-Dienste. Der Warteschlange enthaltenen Builds zeigt der Verzweigung und den Commit, das der Build ausgelöst:

    ![Build in der Warteschlange](media/cicd/build-queued.png)

1. Nachdem der Buildvorgang erfolgreich ist, tritt ein, die Bereitstellung in Azure. Navigieren Sie zu der app im Browser. Beachten Sie, dass der Text "V4" in der Überschrift angezeigt wird:

    ![aktualisierte app](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Überprüfen Sie die Pipeline für die Azure-Pipelines

### <a name="build-definition"></a>Build-Definitionen

Eine Builddefinition wurde erstellt, mit dem Namen *MyFirstProject – ASP.NET Core-CI*. Nach Abschluss des Vorgangs der Build erzeugt eine *ZIP* Datei einschließlich der Ressourcen, die veröffentlicht werden. Diese Ressourcen werden von die releasepipeline in Azure bereitgestellt.

Der Builddefinition **Aufgaben** Registerkarte aufgelistet, die einzelnen Schritte, die verwendet wird. Es gibt fünf Buildaufgaben.

![builddefinitionsaufgaben](media/cicd/build-definition-tasks.png)

1. **Wiederherstellen** &mdash; führt die `dotnet restore` Befehl aus, um die app NuGet-Pakete wiederherzustellen. Das Standardpaket hauptfeed ist "NuGet.org".
1. **Erstellen Sie** &mdash; führt die `dotnet build --configuration release` Befehl aus, um die app Code zu kompilieren. Dies `--configuration` Option wird verwendet, um eine optimierte Version des Codes zu erstellen, das für die Bereitstellung in einer produktionsumgebung geeignet ist. Ändern der *BuildConfiguration* Variable an der Builddefinition **Variablen** Registerkarte, wenn Sie z. B. eine Debug-Konfiguration erforderlich ist.
1. **Test** &mdash; führt die `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` Befehl zum Ausführen der app-Komponententests. Komponententests in alle C#-Projekt entsprechenden ausgeführt werden die `**/*Tests/*.csproj` globmuster. Testergebnisse werden gespeichert, eine *trx* Datei an die vom angegebenen Speicherort der `--results-directory` Option. Wenn alle Tests fehlschlagen, wird der Build schlägt fehl und wird nicht bereitgestellt.

    > [!NOTE]
    > Ändern Sie zum Überprüfen der Unit Tests-Arbeit *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* absichtlich einer der Tests unterbrochen. Ändern Sie z. B. `Assert.True(result.Count > 0);` zu `Assert.False(result.Count > 0);` in die `Returns_News_Stories_Given_Valid_Uri` Methode. Committen Sie und pushen Sie die Änderung in GitHub. Der Build wird ausgelöst, und ein Fehler auftritt. Ändert sich der Status des Build-Pipeline **Fehler**. Die Änderung, Commit und Push Identität erneut zurücksetzen. Der Buildvorgang erfolgreich ist.

1. **Veröffentlichen** &mdash; führt die `dotnet publish --configuration release --output <local_path_on_build_agent>` Befehl erzeugt eine *ZIP* -Datei mit den Elementen, die bereitgestellt werden. Die `--output` Option gibt an, den Ort der Veröffentlichung, der die *ZIP* Datei. Dass der Speicherort angegeben wird, durch das Übergeben einer [vordefinierte Variable](/azure/devops/pipelines/build/variables) mit dem Namen `$(build.artifactstagingdirectory)`. Diese Variable wird erweitert, in einen lokalen Pfad, z. B. *c:\agent\_work\1\a*, auf dem Build-Agent.
1. **Artefakt veröffentlichen** &mdash; Publishes der *ZIP* Datei erstellt hat, indem die **veröffentlichen** Aufgabe. Akzeptiert der Task die *ZIP* Dateispeicherort als Parameter verwendet, die der vordefinierten Variablen ist `$(build.artifactstagingdirectory)`. Die *ZIP* Datei wird als Ordner mit dem Namen veröffentlicht *löschen*.

Klicken Sie auf der Builddefinition **Zusammenfassung** Link, um einen Verlauf der Builds mit der Definition anzuzeigen:

![Screenshot mit Definition buildverlauf](media/cicd/build-definition-summary.png)

Klicken Sie auf die entsprechende Seite auf den Link für die eindeutige Buildnummer:

![Screenshot mit Definition Seite "Buildzusammenfassung"](media/cicd/build-definition-completed.png)

Es wird eine Zusammenfassung von diesem bestimmten Build angezeigt. Klicken Sie auf die **Artefakte** Registerkarte, und beachten Sie die *löschen* wird vom Build erstellten Ordner aufgeführt:

![Screenshot der Artefakte des Builds-Definition - Drop-Ordner](media/cicd/build-definition-artifacts.png)

Verwenden der **herunterladen** und **Durchsuchen** Links, um die veröffentlichten Artefakte zu überprüfen.

### <a name="release-pipeline"></a>Releasepipeline

Eine releasepipeline wurde erstellt, mit dem Namen *MyFirstProject – ASP.NET Core-CD*:

![Screenshot mit –-Pipeline – Übersicht](media/cicd/release-definition-overview.png)

Die zwei wichtigsten Komponenten der releasepipeline sind die **Artefakte** und **Umgebungen**. Klicken Sie auf das Feld in der **Artefakte** Abschnitt zeigt den folgenden Bereich:

![Screenshot mit Pipeline releaseartefakte](media/cicd/release-definition-artifacts.png)

Die **Quelle (Builddefinition)** Wert darstellt, die Builddefinition, die mit dem dieses Release-Pipeline verknüpft ist. Die *ZIP* -Datei, die von einer erfolgreichen Ausführung der Builddefinition wird bereitgestellt, um die *Produktion* Umgebung für die Bereitstellung in Azure. Klicken Sie auf die *-Phase 1, 2 Aufgaben* -link in der *Produktion* Feld der Umgebung zum Anzeigen der Release-Pipeline-Aufgaben:

![Screenshot mit Pipeline releasetasks](media/cicd/release-definition-tasks.png)

Die releasepipeline besteht aus zwei Aufgaben: *Bereitstellen von Azure App Service in Slot* und *Verwalten von Azure App Service – Slotaustausch*. Klicken Sie auf die erste Aufgabe wird die folgende Aufgabenkonfiguration:

![Bereitstellungstask für den Screenshot mit Release-pipeline](media/cicd/release-definition-task1.png)

Die Azure-Abonnement, Diensttyp, Web-app-Name, Ressourcengruppe und bereitstellungsslots werden in der Bereitstellungsaufgabe definiert. Die **Paket oder Ordner** Textfeld enthält die *ZIP* Dateipfad zum extrahiert und bereitgestellt werden die *staging* Steckplatz des der *Mywebapp\<eindeutig a_nzahl\>*  Web-app.

Klicken Sie auf den Slot Swap-Vorgang wird die folgende Aufgabenkonfiguration:

![Screenshot mit Pipeline Slot Swap freigabetask](media/cicd/release-definition-task2.png)

Das Abonnement, Ressourcengruppe, Diensttyp, Web-app-Name und Slot Bereitstellungsdetails werden bereitgestellt. Die **mit Produktion tauschen** Kontrollkästchen ist aktiviert. Daher die Komponenten bereitgestellt die *staging* Slot werden in die produktionsumgebung verlagert wurde.

## <a name="additional-reading"></a>Weiterführende Literatur

* [Erstellen Sie Ihre erste Pipeline mit Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Build und .NET Core-Projekt](/azure/devops/pipelines/languages/dotnet-core)
* [Bereitstellen einer Web-Apps mit Azure-Pipelines](/azure/devops/pipelines/targets/webapp)
