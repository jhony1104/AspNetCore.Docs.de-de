---
title: 'Bereitstellen einer app in App Service: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Bereitstellen einer ASP.NET Core-Apps in Azure App Service, der erste Schritt für DevOps mit ASP.NET Core und Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080440"
---
# <a name="deploy-an-app-to-app-service"></a>Bereitstellen einer app in App Service

[Azure App Service](/azure/app-service/) ist der Azure web-hosting-Plattform. Bereitstellen einer Web-app in Azure App Service kann manuell oder durch ein automatisierter Prozess erfolgen. In diesem Abschnitt des Handbuchs erläutert Bereitstellungsmethoden zur Verfügung, die ausgelöst werden können, manuell oder mithilfe eines Skripts, die über die Befehlszeile oder ausgelöst wird, manuell mithilfe von Visual Studio.

In diesem Abschnitt müssen Sie die folgenden Aufgaben auszuführen:

* Herunterladen Sie, und erstellen Sie die Beispiel-app.
* Erstellen Sie eine Azure App Service Web App mithilfe von Azure Cloud Shell ein.
* Bereitstellen der Beispiel-app in Azure mithilfe von Git.
* Stellen Sie eine Änderung in der app, die mithilfe von Visual Studio an.
* Fügen Sie einen stagingslot für die Web-app hinzu.
* Stellen Sie ein Update im stagingslot bereit.
* Tauschen Sie die Staging- und produktionsslots.

## <a name="download-and-test-the-app"></a>Herunterladen und Testen der app

Die app, die in diesem Handbuch verwendet wird, eine vorab erstellte ASP.NET Core-app [einfache Feed-Reader](https://github.com/Azure-Samples/simple-feed-reader/). Es ist eine Razor-Seiten-app, verwendet der `Microsoft.SyndicationFeed.ReaderWriter` -API, um ein RSS/Atom-Feed abrufen, und zeigen die Newselemente in einer Liste.

Sie können den Code überprüfen, aber es ist wichtig zu verstehen, dass es nichts Besonderes zu dieser app ist. Es ist nur eine einfache ASP.NET Core-app zur Veranschaulichung.

Herunterladen Sie über eine Befehlsshell den Code, erstellen Sie das Projekt, und führen Sie es wie folgt aus.

> *Hinweis: Linux/macOS-Benutzer sollten geeignete Änderungen an Pfaden vornehmen, z. b. mithilfe eines`/`Schrägstrichs () anstelle`\`eines umgekehrten Schrägstrichs ().*

1. Klonen Sie den Code in einen Ordner auf Ihrem lokalen Computer.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Ändern Sie den Arbeitsordner auf der *Simple-Feed-Reader* erstellt haben.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Wiederherstellen Sie der Pakete, und erstellen Sie die Projektmappe.

    ```dotnetcli
    dotnet build
    ```

4. Führen Sie die App aus.

    ```dotnetcli
    dotnet run
    ```

    ![Der Dotnet run-Befehl ist erfolgreich.](./media/deploying-to-app-service/dotnet-run.png)

5. Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:5000`. Die app Ihnen die Möglichkeit zu geben oder fügen einen Syndication-feed-URL und eine Liste der Nachrichtenelemente anzuzeigen.

     ![Die app zeigt den Inhalt eines RSS-feed](./media/deploying-to-app-service/app-in-browser.png)

6. Sobald Sie zufrieden sind wird die app ordnungsgemäß funktioniert, fahren Sie es durch Drücken von **STRG**+**C** in der Befehlsshell.

## <a name="create-the-azure-app-service-web-app"></a>Erstellen Sie die Azure App Service-Web-App

Um die app bereitstellen, müssen Sie zum Erstellen einer App Service [Web-App](/azure/app-service/app-service-web-overview). Nach der Erstellung der Web-App stellen Sie sich darauf aus Ihrem lokalen Computer mit Git bereit.

1. Melden Sie sich bei der [Azure Cloudshell](https://shell.azure.com/bash). Hinweis: Wenn Sie sich erstmalig anmelden, werden Cloud Shell aufgefordert, ein Speicherkonto für Konfigurationsdateien zu erstellen. Akzeptieren Sie die Standardeinstellungen, oder geben Sie einen eindeutigen Namen.

2. Verwenden Sie Cloud Shell, für die folgenden Schritte aus.

    a. Deklarieren Sie eine Variable zum Speichern Ihrer Web-app-Namen ein. Der Name muss in der Standard-URL zu verwendende eindeutig sein. Mithilfe der `$RANDOM` Bash-Funktion, um den Namen zu erstellen, stellt Eindeutigkeit sicher und im Format führt `webappname99999`.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Erstellen Sie eine Ressourcengruppe aus. Ressourcengruppen bieten eine Möglichkeit zum Aggregieren von Azure-Ressourcen als Gruppe verwaltet werden.

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    Die `az` Befehl ruft die [Azure-Befehlszeilenschnittstelle](/cli/azure/). Die CLI kann lokal ausgeführt werden, jedoch Zeit und die Konfiguration speichert sie in der Cloud Shell verwenden.

    c. Erstellen Sie einen App Service-Plan im Tarif S1. App Service-Plan ist eine Gruppierung von Web-apps, die den gleichen Tarif gemeinsam nutzen. Der S1-Tarif nicht kostenlos, aber es ist erforderlich, für die staging-Slots-Funktion.

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Erstellen Sie die Web-app-Ressource mithilfe von App Service-Plan in derselben Ressourcengruppe befinden.

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Legen Sie die Anmeldeinformationen für die Bereitstellung. Diese Anmeldeinformationen für die Bereitstellung gelten für alle Web-apps in Ihrem Abonnement. Verwenden Sie in der Benutzername keine Sonderzeichen.

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Konfigurieren der Web-app zum Akzeptieren von Bereitstellungen aus dem lokalen Git und der Anzeige der *Git-bereitstellungs-URL*. **Beachten Sie diese URL später zu Referenzzwecken**.

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Anzeigen der *web-app-URL*. Navigieren Sie zu diese URL in die leere Web-app finden Sie unter. **Beachten Sie diese URL später zu Referenzzwecken**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Eine Befehls-Shell auf dem lokalen Computer, navigieren Sie zu der Web-app-Projektordner (z. B. `.\simple-feed-reader\SimpleFeedReader`). Führen Sie die folgenden Befehle zum Git einrichten, um die bereitstellungs-URL mithilfe von Push übertragen:

    a. Fügen Sie die remote-URL, an das lokale Repository.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Mithilfe von Push übertragen den lokalen *master* branch der *Azure-Prod-* des Remote- *master* Branch.

    ```console
    git push azure-prod master
    ```

    Sie werden Anmeldeinformationen für die Bereitstellung aufgefordert werden, die Sie zuvor erstellt haben. Beachten Sie die Ausgabe in der Befehlsshell. Azure wird die ASP.NET Core-app Remote erstellt.

4. In einem Browser, navigieren Sie zu der *Web-app-URL* und notieren Sie sich die app erstellt und bereitgestellt wurde. Weitere Änderungen können in der lokalen Git-Repository mit übernommen werden `git commit`. Diese Änderungen werden per Push an Azure mit der vorhergehenden `git push` Befehl.

## <a name="deployment-with-visual-studio"></a>Bereitstellung mit Visual Studio

> *Hinweis: Dieser Abschnitt gilt nur für Windows. Linux und MacOS-Benutzer sollte es sich um die in Schritt 2 unten beschriebenen Änderung vornehmen. Speichern Sie die Datei, und committen der Änderung im lokalen Repository mit `git commit`. Drücken Sie schließlich die Änderung mit `git push`, wie im ersten Abschnitt.*

Die app wurde bereits von der Befehlsshell aus bereitgestellt wurde. Integrierte Tools für Visual Studio können wir ein Update für die app bereit. Hinter den Kulissen führt Visual Studio das gleiche wie die Befehlszeilentools, jedoch innerhalb von Visual Studio vertraute Benutzeroberfläche.

1. Open *SimpleFeedReader.sln* in Visual Studio.
2. Öffnen Sie im Projektmappen-Explorer *Pages\Index.cshtml*. Änderung `<h2>Simple Feed Reader</h2>` zu `<h2>Simple Feed Reader - V2</h2>`.
3. Drücken Sie **STRG**+**UMSCHALT**+**B** zum Erstellen der app.
4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **veröffentlichen**.

    ![Screenshot der mit der rechten Maustaste, und veröffentlichen](./media/deploying-to-app-service/publish.png)
5. Visual Studio kann eine neue App Service-Ressource erstellen, aber über die vorhandene Bereitstellung dieses Update veröffentlicht werden. In der **Veröffentlichungsziel** wählen Sie im Dialogfeld **App Service** aus der Liste auf der linken Seite, und wählen Sie dann **vorhandene auswählen**. Klicken Sie auf **Veröffentlichen**.
6. In der **App Service** Dialogfeld bestätigen, dass die Microsoft- oder Organisationskonto an, die zum Erstellen von Ihrem Azure-Abonnement in der oberen rechten Ecke angezeigt wird. Ist dies nicht, klicken Sie auf der Dropdownliste aus, und fügen Sie es hinzu.
7. Überprüfen Sie, ob die richtige Azure **Abonnement** ausgewählt ist. Für **Ansicht**Option **Ressourcengruppe**. Erweitern Sie die **AzureTutorial** Ressourcengruppe aus, und wählen Sie dann die vorhandene Web-app. Klicken Sie auf **OK**.

    ![Screenshot mit Publish App Service-Dialogfeld](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio erstellt und die app in Azure bereitgestellt wird. Navigieren Sie zu der URL der Web-app. Überprüfen Sie, die die `<h2>` elementänderung ist.

![Die app mit der der Titel wurde geändert](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Bereitstellungsslots

Bereitstellungsslots unterstützen das Staging der Änderungen ohne Auswirkungen auf die app in der Produktion ausgeführt wird. Sobald die bereitgestellte Version der app von einem Quality Assurance-Team überprüft wird, können die Produktions- und stagingslots ausgetauscht werden. Die app in der Stagingumgebung wird in der produktionsumgebung auf diese Weise höher gestuft. Die folgenden Schritte aus einen stagingslot erstellen, ihm einige Änderungen bereitstellen und überführt den stagingslot mit Produktion nach der Überprüfung.

1. Melden Sie sich bei der [Azure Cloud Shell](https://shell.azure.com/bash), sofern nicht bereits angemeldet.
2. Erstellen Sie die staging-Slot.

    a. Erstellen Sie einen bereitstellungsslot mit dem Namen *staging*.

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Konfigurieren Sie den stagingslot Verwendung Bereitstellung über die lokale Git, und erhalten die **staging** bereitstellungs-URL. **Beachten Sie diese URL später zu Referenzzwecken**.

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Der stagingslot-URL angezeigt. Navigieren Sie zu die URL für das leere Einschubfach aus staging finden Sie unter. **Beachten Sie diese URL später zu Referenzzwecken**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Ändern Sie in einem Text-Editor oder Visual Studio, *Pages/Index.cshtml* erneut, damit die `<h2>` Element liest `<h2>Simple Feed Reader - V3</h2>` und speichern Sie die Datei.

4. Committen Sie die Datei in der lokalen Git-Repository mit den **Änderungen** Seite in Visual Studio *Team Explorer* Registerkarte oder durch Eingabe der folgenden Befehlsshell des lokalen Computers:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Mithilfe des lokalen Computers Befehlsshell fügen Sie die staging-URL als ein Git-Remoteverzeichnis hinzu, und pushen der Änderungen mit ausgeführtem Commit:

    a. Fügen Sie die remote-URL für das Staging an das lokale Git-Repository hinzu.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Mithilfe von Push übertragen den lokalen *master* branch der *Azure-Staging* des Remote- *master* Branch.

    ```console
    git push azure-staging master
    ```

    Warten Sie, während es sich bei Azure erstellt und die app bereitgestellt wird.

6. Öffnen Sie zwei Browserfenster, um sicherzustellen, dass V3 im stagingslot bereitgestellt wurde. Navigieren Sie in einem Fenster zu öffnen zu der ursprünglichen URL der Web-app. Navigieren Sie zu der staging-Web-app-URL, in dem anderen Fenster. Produktions-URL dient V2 der app. Die staging-URL dient V3 der app.

    ![Screenshot, der die Browserfenster vergleichen](./media/deploying-to-app-service/ready-to-swap.png)

7. Wechseln Sie in der Cloud Shell im stagingslot überprüft/vorbereitet-nach-oben in der Produktion.

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Stellen Sie sicher, dass der Austausch aufgetreten ist, indem Sie zwei Browserfenster aktualisieren.

    ![Vergleichen das Browserfenster, nach dem Austausch](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Zusammenfassung

In diesem Abschnitt wurden die folgenden Aufgaben ausgeführt:

* Heruntergeladen und die Beispiel-app erstellt.
* Erstellt eine Azure App Service Web App mithilfe von Azure Cloud Shell.
* Die Beispiel-app bereitgestellt in Azure mithilfe von Git.
* Eine Änderung bereitgestellt in der app mithilfe von Visual Studio.
* Einen staging-Slot und die Web-app hinzugefügt.
* Ein Update bereitgestellt im stagingslot.
* Die Staging- und produktionsslots, ausgetauscht.

Im nächsten Abschnitt erfahren Sie, wie Sie eine DevOps-Pipeline mit Azure-Pipelines zu erstellen.

## <a name="additional-reading"></a>Weiterführende Literatur

* [Web-Apps – Übersicht](/azure/app-service/app-service-web-overview)
* [Erstellen einer .NET Core- und SQL-Datenbank-Web-Apps in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Konfigurieren Sie Anmeldeinformationen für die Bereitstellung für Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Einrichten von Stagingumgebungen in Azure App Service](/azure/app-service/web-sites-staged-publishing)
