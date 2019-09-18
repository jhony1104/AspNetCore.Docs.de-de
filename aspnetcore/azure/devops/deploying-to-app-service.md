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
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="df910-103">Bereitstellen einer app in App Service</span><span class="sxs-lookup"><span data-stu-id="df910-103">Deploy an app to App Service</span></span>

<span data-ttu-id="df910-104">[Azure App Service](/azure/app-service/) ist der Azure web-hosting-Plattform.</span><span class="sxs-lookup"><span data-stu-id="df910-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="df910-105">Bereitstellen einer Web-app in Azure App Service kann manuell oder durch ein automatisierter Prozess erfolgen.</span><span class="sxs-lookup"><span data-stu-id="df910-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="df910-106">In diesem Abschnitt des Handbuchs erläutert Bereitstellungsmethoden zur Verfügung, die ausgelöst werden können, manuell oder mithilfe eines Skripts, die über die Befehlszeile oder ausgelöst wird, manuell mithilfe von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="df910-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="df910-107">In diesem Abschnitt müssen Sie die folgenden Aufgaben auszuführen:</span><span class="sxs-lookup"><span data-stu-id="df910-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="df910-108">Herunterladen Sie, und erstellen Sie die Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="df910-108">Download and build the sample app.</span></span>
* <span data-ttu-id="df910-109">Erstellen Sie eine Azure App Service Web App mithilfe von Azure Cloud Shell ein.</span><span class="sxs-lookup"><span data-stu-id="df910-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="df910-110">Bereitstellen der Beispiel-app in Azure mithilfe von Git.</span><span class="sxs-lookup"><span data-stu-id="df910-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="df910-111">Stellen Sie eine Änderung in der app, die mithilfe von Visual Studio an.</span><span class="sxs-lookup"><span data-stu-id="df910-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="df910-112">Fügen Sie einen stagingslot für die Web-app hinzu.</span><span class="sxs-lookup"><span data-stu-id="df910-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="df910-113">Stellen Sie ein Update im stagingslot bereit.</span><span class="sxs-lookup"><span data-stu-id="df910-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="df910-114">Tauschen Sie die Staging- und produktionsslots.</span><span class="sxs-lookup"><span data-stu-id="df910-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="df910-115">Herunterladen und Testen der app</span><span class="sxs-lookup"><span data-stu-id="df910-115">Download and test the app</span></span>

<span data-ttu-id="df910-116">Die app, die in diesem Handbuch verwendet wird, eine vorab erstellte ASP.NET Core-app [einfache Feed-Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="df910-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="df910-117">Es ist eine Razor-Seiten-app, verwendet der `Microsoft.SyndicationFeed.ReaderWriter` -API, um ein RSS/Atom-Feed abrufen, und zeigen die Newselemente in einer Liste.</span><span class="sxs-lookup"><span data-stu-id="df910-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="df910-118">Sie können den Code überprüfen, aber es ist wichtig zu verstehen, dass es nichts Besonderes zu dieser app ist.</span><span class="sxs-lookup"><span data-stu-id="df910-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="df910-119">Es ist nur eine einfache ASP.NET Core-app zur Veranschaulichung.</span><span class="sxs-lookup"><span data-stu-id="df910-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="df910-120">Herunterladen Sie über eine Befehlsshell den Code, erstellen Sie das Projekt, und führen Sie es wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="df910-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="df910-121">*Hinweis: Linux/macOS-Benutzer sollten geeignete Änderungen an Pfaden vornehmen, z. b. mithilfe eines`/`Schrägstrichs () anstelle`\`eines umgekehrten Schrägstrichs ().*</span><span class="sxs-lookup"><span data-stu-id="df910-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="df910-122">Klonen Sie den Code in einen Ordner auf Ihrem lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="df910-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="df910-123">Ändern Sie den Arbeitsordner auf der *Simple-Feed-Reader* erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="df910-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="df910-124">Wiederherstellen Sie der Pakete, und erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="df910-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="df910-125">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="df910-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Der Dotnet run-Befehl ist erfolgreich.](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="df910-127">Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="df910-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="df910-128">Die app Ihnen die Möglichkeit zu geben oder fügen einen Syndication-feed-URL und eine Liste der Nachrichtenelemente anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="df910-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Die app zeigt den Inhalt eines RSS-feed](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="df910-130">Sobald Sie zufrieden sind wird die app ordnungsgemäß funktioniert, fahren Sie es durch Drücken von **STRG**+**C** in der Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="df910-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="df910-131">Erstellen Sie die Azure App Service-Web-App</span><span class="sxs-lookup"><span data-stu-id="df910-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="df910-132">Um die app bereitstellen, müssen Sie zum Erstellen einer App Service [Web-App](/azure/app-service/app-service-web-overview).</span><span class="sxs-lookup"><span data-stu-id="df910-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="df910-133">Nach der Erstellung der Web-App stellen Sie sich darauf aus Ihrem lokalen Computer mit Git bereit.</span><span class="sxs-lookup"><span data-stu-id="df910-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="df910-134">Melden Sie sich bei der [Azure Cloudshell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="df910-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="df910-135">Hinweis: Wenn Sie sich erstmalig anmelden, werden Cloud Shell aufgefordert, ein Speicherkonto für Konfigurationsdateien zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="df910-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="df910-136">Akzeptieren Sie die Standardeinstellungen, oder geben Sie einen eindeutigen Namen.</span><span class="sxs-lookup"><span data-stu-id="df910-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="df910-137">Verwenden Sie Cloud Shell, für die folgenden Schritte aus.</span><span class="sxs-lookup"><span data-stu-id="df910-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="df910-138">a.</span><span class="sxs-lookup"><span data-stu-id="df910-138">a.</span></span> <span data-ttu-id="df910-139">Deklarieren Sie eine Variable zum Speichern Ihrer Web-app-Namen ein.</span><span class="sxs-lookup"><span data-stu-id="df910-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="df910-140">Der Name muss in der Standard-URL zu verwendende eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="df910-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="df910-141">Mithilfe der `$RANDOM` Bash-Funktion, um den Namen zu erstellen, stellt Eindeutigkeit sicher und im Format führt `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="df910-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="df910-142">b.</span><span class="sxs-lookup"><span data-stu-id="df910-142">b.</span></span> <span data-ttu-id="df910-143">Erstellen Sie eine Ressourcengruppe aus.</span><span class="sxs-lookup"><span data-stu-id="df910-143">Create a resource group.</span></span> <span data-ttu-id="df910-144">Ressourcengruppen bieten eine Möglichkeit zum Aggregieren von Azure-Ressourcen als Gruppe verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="df910-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="df910-145">Die `az` Befehl ruft die [Azure-Befehlszeilenschnittstelle](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="df910-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="df910-146">Die CLI kann lokal ausgeführt werden, jedoch Zeit und die Konfiguration speichert sie in der Cloud Shell verwenden.</span><span class="sxs-lookup"><span data-stu-id="df910-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="df910-147">c.</span><span class="sxs-lookup"><span data-stu-id="df910-147">c.</span></span> <span data-ttu-id="df910-148">Erstellen Sie einen App Service-Plan im Tarif S1.</span><span class="sxs-lookup"><span data-stu-id="df910-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="df910-149">App Service-Plan ist eine Gruppierung von Web-apps, die den gleichen Tarif gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="df910-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="df910-150">Der S1-Tarif nicht kostenlos, aber es ist erforderlich, für die staging-Slots-Funktion.</span><span class="sxs-lookup"><span data-stu-id="df910-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="df910-151">d.</span><span class="sxs-lookup"><span data-stu-id="df910-151">d.</span></span> <span data-ttu-id="df910-152">Erstellen Sie die Web-app-Ressource mithilfe von App Service-Plan in derselben Ressourcengruppe befinden.</span><span class="sxs-lookup"><span data-stu-id="df910-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="df910-153">e.</span><span class="sxs-lookup"><span data-stu-id="df910-153">e.</span></span> <span data-ttu-id="df910-154">Legen Sie die Anmeldeinformationen für die Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="df910-154">Set the deployment credentials.</span></span> <span data-ttu-id="df910-155">Diese Anmeldeinformationen für die Bereitstellung gelten für alle Web-apps in Ihrem Abonnement.</span><span class="sxs-lookup"><span data-stu-id="df910-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="df910-156">Verwenden Sie in der Benutzername keine Sonderzeichen.</span><span class="sxs-lookup"><span data-stu-id="df910-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="df910-157">f.</span><span class="sxs-lookup"><span data-stu-id="df910-157">f.</span></span> <span data-ttu-id="df910-158">Konfigurieren der Web-app zum Akzeptieren von Bereitstellungen aus dem lokalen Git und der Anzeige der *Git-bereitstellungs-URL*.</span><span class="sxs-lookup"><span data-stu-id="df910-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="df910-159">**Beachten Sie diese URL später zu Referenzzwecken**.</span><span class="sxs-lookup"><span data-stu-id="df910-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="df910-160">g.</span><span class="sxs-lookup"><span data-stu-id="df910-160">g.</span></span> <span data-ttu-id="df910-161">Anzeigen der *web-app-URL*.</span><span class="sxs-lookup"><span data-stu-id="df910-161">Display the *web app URL*.</span></span> <span data-ttu-id="df910-162">Navigieren Sie zu diese URL in die leere Web-app finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="df910-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="df910-163">**Beachten Sie diese URL später zu Referenzzwecken**.</span><span class="sxs-lookup"><span data-stu-id="df910-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="df910-164">Eine Befehls-Shell auf dem lokalen Computer, navigieren Sie zu der Web-app-Projektordner (z. B. `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="df910-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="df910-165">Führen Sie die folgenden Befehle zum Git einrichten, um die bereitstellungs-URL mithilfe von Push übertragen:</span><span class="sxs-lookup"><span data-stu-id="df910-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="df910-166">a.</span><span class="sxs-lookup"><span data-stu-id="df910-166">a.</span></span> <span data-ttu-id="df910-167">Fügen Sie die remote-URL, an das lokale Repository.</span><span class="sxs-lookup"><span data-stu-id="df910-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="df910-168">b.</span><span class="sxs-lookup"><span data-stu-id="df910-168">b.</span></span> <span data-ttu-id="df910-169">Mithilfe von Push übertragen den lokalen *master* branch der *Azure-Prod-* des Remote- *master* Branch.</span><span class="sxs-lookup"><span data-stu-id="df910-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="df910-170">Sie werden Anmeldeinformationen für die Bereitstellung aufgefordert werden, die Sie zuvor erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="df910-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="df910-171">Beachten Sie die Ausgabe in der Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="df910-171">Observe the output in the command shell.</span></span> <span data-ttu-id="df910-172">Azure wird die ASP.NET Core-app Remote erstellt.</span><span class="sxs-lookup"><span data-stu-id="df910-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="df910-173">In einem Browser, navigieren Sie zu der *Web-app-URL* und notieren Sie sich die app erstellt und bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="df910-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="df910-174">Weitere Änderungen können in der lokalen Git-Repository mit übernommen werden `git commit`.</span><span class="sxs-lookup"><span data-stu-id="df910-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="df910-175">Diese Änderungen werden per Push an Azure mit der vorhergehenden `git push` Befehl.</span><span class="sxs-lookup"><span data-stu-id="df910-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="df910-176">Bereitstellung mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df910-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="df910-177">*Hinweis: Dieser Abschnitt gilt nur für Windows. Linux und MacOS-Benutzer sollte es sich um die in Schritt 2 unten beschriebenen Änderung vornehmen. Speichern Sie die Datei, und committen der Änderung im lokalen Repository mit `git commit`. Drücken Sie schließlich die Änderung mit `git push`, wie im ersten Abschnitt.*</span><span class="sxs-lookup"><span data-stu-id="df910-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="df910-178">Die app wurde bereits von der Befehlsshell aus bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="df910-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="df910-179">Integrierte Tools für Visual Studio können wir ein Update für die app bereit.</span><span class="sxs-lookup"><span data-stu-id="df910-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="df910-180">Hinter den Kulissen führt Visual Studio das gleiche wie die Befehlszeilentools, jedoch innerhalb von Visual Studio vertraute Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="df910-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="df910-181">Open *SimpleFeedReader.sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="df910-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="df910-182">Öffnen Sie im Projektmappen-Explorer *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="df910-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="df910-183">Änderung `<h2>Simple Feed Reader</h2>` zu `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="df910-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="df910-184">Drücken Sie **STRG**+**UMSCHALT**+**B** zum Erstellen der app.</span><span class="sxs-lookup"><span data-stu-id="df910-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="df910-185">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="df910-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Screenshot der mit der rechten Maustaste, und veröffentlichen](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="df910-187">Visual Studio kann eine neue App Service-Ressource erstellen, aber über die vorhandene Bereitstellung dieses Update veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="df910-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="df910-188">In der **Veröffentlichungsziel** wählen Sie im Dialogfeld **App Service** aus der Liste auf der linken Seite, und wählen Sie dann **vorhandene auswählen**.</span><span class="sxs-lookup"><span data-stu-id="df910-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="df910-189">Klicken Sie auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="df910-189">Click **Publish**.</span></span>
6. <span data-ttu-id="df910-190">In der **App Service** Dialogfeld bestätigen, dass die Microsoft- oder Organisationskonto an, die zum Erstellen von Ihrem Azure-Abonnement in der oberen rechten Ecke angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="df910-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="df910-191">Ist dies nicht, klicken Sie auf der Dropdownliste aus, und fügen Sie es hinzu.</span><span class="sxs-lookup"><span data-stu-id="df910-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="df910-192">Überprüfen Sie, ob die richtige Azure **Abonnement** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="df910-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="df910-193">Für **Ansicht**Option **Ressourcengruppe**.</span><span class="sxs-lookup"><span data-stu-id="df910-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="df910-194">Erweitern Sie die **AzureTutorial** Ressourcengruppe aus, und wählen Sie dann die vorhandene Web-app.</span><span class="sxs-lookup"><span data-stu-id="df910-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="df910-195">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="df910-195">Click **OK**.</span></span>

    ![Screenshot mit Publish App Service-Dialogfeld](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="df910-197">Visual Studio erstellt und die app in Azure bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="df910-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="df910-198">Navigieren Sie zu der URL der Web-app.</span><span class="sxs-lookup"><span data-stu-id="df910-198">Browse to the web app URL.</span></span> <span data-ttu-id="df910-199">Überprüfen Sie, die die `<h2>` elementänderung ist.</span><span class="sxs-lookup"><span data-stu-id="df910-199">Validate that the `<h2>` element modification is live.</span></span>

![Die app mit der der Titel wurde geändert](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="df910-201">Bereitstellungsslots</span><span class="sxs-lookup"><span data-stu-id="df910-201">Deployment slots</span></span>

<span data-ttu-id="df910-202">Bereitstellungsslots unterstützen das Staging der Änderungen ohne Auswirkungen auf die app in der Produktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="df910-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="df910-203">Sobald die bereitgestellte Version der app von einem Quality Assurance-Team überprüft wird, können die Produktions- und stagingslots ausgetauscht werden.</span><span class="sxs-lookup"><span data-stu-id="df910-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="df910-204">Die app in der Stagingumgebung wird in der produktionsumgebung auf diese Weise höher gestuft.</span><span class="sxs-lookup"><span data-stu-id="df910-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="df910-205">Die folgenden Schritte aus einen stagingslot erstellen, ihm einige Änderungen bereitstellen und überführt den stagingslot mit Produktion nach der Überprüfung.</span><span class="sxs-lookup"><span data-stu-id="df910-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="df910-206">Melden Sie sich bei der [Azure Cloud Shell](https://shell.azure.com/bash), sofern nicht bereits angemeldet.</span><span class="sxs-lookup"><span data-stu-id="df910-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="df910-207">Erstellen Sie die staging-Slot.</span><span class="sxs-lookup"><span data-stu-id="df910-207">Create the staging slot.</span></span>

    <span data-ttu-id="df910-208">a.</span><span class="sxs-lookup"><span data-stu-id="df910-208">a.</span></span> <span data-ttu-id="df910-209">Erstellen Sie einen bereitstellungsslot mit dem Namen *staging*.</span><span class="sxs-lookup"><span data-stu-id="df910-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="df910-210">b.</span><span class="sxs-lookup"><span data-stu-id="df910-210">b.</span></span> <span data-ttu-id="df910-211">Konfigurieren Sie den stagingslot Verwendung Bereitstellung über die lokale Git, und erhalten die **staging** bereitstellungs-URL.</span><span class="sxs-lookup"><span data-stu-id="df910-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="df910-212">**Beachten Sie diese URL später zu Referenzzwecken**.</span><span class="sxs-lookup"><span data-stu-id="df910-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="df910-213">c.</span><span class="sxs-lookup"><span data-stu-id="df910-213">c.</span></span> <span data-ttu-id="df910-214">Der stagingslot-URL angezeigt.</span><span class="sxs-lookup"><span data-stu-id="df910-214">Display the staging slot's URL.</span></span> <span data-ttu-id="df910-215">Navigieren Sie zu die URL für das leere Einschubfach aus staging finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="df910-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="df910-216">**Beachten Sie diese URL später zu Referenzzwecken**.</span><span class="sxs-lookup"><span data-stu-id="df910-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="df910-217">Ändern Sie in einem Text-Editor oder Visual Studio, *Pages/Index.cshtml* erneut, damit die `<h2>` Element liest `<h2>Simple Feed Reader - V3</h2>` und speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="df910-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="df910-218">Committen Sie die Datei in der lokalen Git-Repository mit den **Änderungen** Seite in Visual Studio *Team Explorer* Registerkarte oder durch Eingabe der folgenden Befehlsshell des lokalen Computers:</span><span class="sxs-lookup"><span data-stu-id="df910-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="df910-219">Mithilfe des lokalen Computers Befehlsshell fügen Sie die staging-URL als ein Git-Remoteverzeichnis hinzu, und pushen der Änderungen mit ausgeführtem Commit:</span><span class="sxs-lookup"><span data-stu-id="df910-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="df910-220">a.</span><span class="sxs-lookup"><span data-stu-id="df910-220">a.</span></span> <span data-ttu-id="df910-221">Fügen Sie die remote-URL für das Staging an das lokale Git-Repository hinzu.</span><span class="sxs-lookup"><span data-stu-id="df910-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="df910-222">b.</span><span class="sxs-lookup"><span data-stu-id="df910-222">b.</span></span> <span data-ttu-id="df910-223">Mithilfe von Push übertragen den lokalen *master* branch der *Azure-Staging* des Remote- *master* Branch.</span><span class="sxs-lookup"><span data-stu-id="df910-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="df910-224">Warten Sie, während es sich bei Azure erstellt und die app bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="df910-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="df910-225">Öffnen Sie zwei Browserfenster, um sicherzustellen, dass V3 im stagingslot bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="df910-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="df910-226">Navigieren Sie in einem Fenster zu öffnen zu der ursprünglichen URL der Web-app.</span><span class="sxs-lookup"><span data-stu-id="df910-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="df910-227">Navigieren Sie zu der staging-Web-app-URL, in dem anderen Fenster.</span><span class="sxs-lookup"><span data-stu-id="df910-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="df910-228">Produktions-URL dient V2 der app.</span><span class="sxs-lookup"><span data-stu-id="df910-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="df910-229">Die staging-URL dient V3 der app.</span><span class="sxs-lookup"><span data-stu-id="df910-229">The staging URL serves V3 of the app.</span></span>

    ![Screenshot, der die Browserfenster vergleichen](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="df910-231">Wechseln Sie in der Cloud Shell im stagingslot überprüft/vorbereitet-nach-oben in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="df910-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="df910-232">Stellen Sie sicher, dass der Austausch aufgetreten ist, indem Sie zwei Browserfenster aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="df910-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Vergleichen das Browserfenster, nach dem Austausch](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="df910-234">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="df910-234">Summary</span></span>

<span data-ttu-id="df910-235">In diesem Abschnitt wurden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="df910-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="df910-236">Heruntergeladen und die Beispiel-app erstellt.</span><span class="sxs-lookup"><span data-stu-id="df910-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="df910-237">Erstellt eine Azure App Service Web App mithilfe von Azure Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="df910-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="df910-238">Die Beispiel-app bereitgestellt in Azure mithilfe von Git.</span><span class="sxs-lookup"><span data-stu-id="df910-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="df910-239">Eine Änderung bereitgestellt in der app mithilfe von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="df910-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="df910-240">Einen staging-Slot und die Web-app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="df910-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="df910-241">Ein Update bereitgestellt im stagingslot.</span><span class="sxs-lookup"><span data-stu-id="df910-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="df910-242">Die Staging- und produktionsslots, ausgetauscht.</span><span class="sxs-lookup"><span data-stu-id="df910-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="df910-243">Im nächsten Abschnitt erfahren Sie, wie Sie eine DevOps-Pipeline mit Azure-Pipelines zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="df910-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="df910-244">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="df910-244">Additional reading</span></span>

* [<span data-ttu-id="df910-245">Web-Apps – Übersicht</span><span class="sxs-lookup"><span data-stu-id="df910-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="df910-246">Erstellen einer .NET Core- und SQL-Datenbank-Web-Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="df910-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="df910-247">Konfigurieren Sie Anmeldeinformationen für die Bereitstellung für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="df910-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="df910-248">Einrichten von Stagingumgebungen in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="df910-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
