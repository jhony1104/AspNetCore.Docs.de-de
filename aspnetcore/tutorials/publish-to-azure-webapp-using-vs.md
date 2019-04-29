---
title: Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 98dea9cfbafb53de8ba82ef06f4885d9fbf50123
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705616"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="22e48-103">Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="22e48-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="22e48-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="22e48-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="22e48-105">Lesen Sie [Publish to Azure from Visual Studio for Mac (Veröffentlichen in Azure aus Visual Studio für Mac)](https://blog.xamarin.com/publish-azure-visual-studio-mac/), wenn Sie unter macOS arbeiten.</span><span class="sxs-lookup"><span data-stu-id="22e48-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on macOS.</span></span>

<span data-ttu-id="22e48-106">Informationen zur Problembehandlung bei der App Service-Bereitstellung finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="22e48-106">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="set-up"></a><span data-ttu-id="22e48-107">Einrichten</span><span class="sxs-lookup"><span data-stu-id="22e48-107">Set up</span></span>

* <span data-ttu-id="22e48-108">Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.</span><span class="sxs-lookup"><span data-stu-id="22e48-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="22e48-109">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="22e48-109">Create a web app</span></span>

<span data-ttu-id="22e48-110">Klicken Sie auf der Startseite von Visual Studio auf **Datei > Neu > Projekt...**.</span><span class="sxs-lookup"><span data-stu-id="22e48-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Datei (Menü)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="22e48-112">Schließen Sie das Dialogfeld **Neues Projekt**ab:</span><span class="sxs-lookup"><span data-stu-id="22e48-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="22e48-113">Wählen Sie im linken Bereich **.NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="22e48-114">Wählen Sie im mittleren Bereich **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="22e48-115">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="22e48-115">Select **OK**.</span></span>

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="22e48-117">Gehen Sie im Dialogfeld **ASP.NET Core-Webanwendung** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="22e48-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="22e48-118">Wählen Sie **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-118">Select **Web Application**.</span></span>
* <span data-ttu-id="22e48-119">Wählen Sie **Authentifizierung ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-119">Select **Change Authentication**.</span></span>

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="22e48-121">Das Dialogfeld **Authentifizierung ändern** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22e48-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="22e48-122">Wählen Sie **Einzelne Benutzerkonten** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="22e48-123">Klicken Sie auf **OK**, um zur Seite **Neue ASP.NET Core-Webanwendung** zurückzukehren, und klicken Sie anschließend noch einmal auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="22e48-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Dialogfeld „New ASP.NET Core Web Authentication“ (Neue ASP.NET Core-Webauthentifizierung)](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="22e48-125">Visual Studio erstellt die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="22e48-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="22e48-126">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="22e48-126">Run the app</span></span>

* <span data-ttu-id="22e48-127">Drücken Sie STRG+F5, um das Projekt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="22e48-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="22e48-128">Testen Sie die Links **About** (Info) und **Contact** (Kontakt).</span><span class="sxs-lookup"><span data-stu-id="22e48-128">Test the **About** and **Contact** links.</span></span>

![Auf „localhost“ in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="22e48-130">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="22e48-130">Register a user</span></span>

* <span data-ttu-id="22e48-131">Wählen Sie **Registrieren**, und registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="22e48-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="22e48-132">Sie können eine fiktive E-Mail-Adresse verwenden.</span><span class="sxs-lookup"><span data-stu-id="22e48-132">You can use a fictitious email address.</span></span> <span data-ttu-id="22e48-133">Sobald Sie diese übermitteln, zeigt die Seite die folgende Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="22e48-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="22e48-134">*„Interner Serverfehler: Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. SQL-Ausnahme: Die Datenbank kann nicht geöffnet werden. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.“*</span><span class="sxs-lookup"><span data-stu-id="22e48-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="22e48-135">Wählen Sie **Migrationen anwenden** aus, und aktualisieren Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="22e48-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Interner Serverfehler: Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="22e48-139">Die App zeigt die zum Registrieren des neuen Benutzers verwendete E-Mail-Adresse und den Link **Abmelden**.</span><span class="sxs-lookup"><span data-stu-id="22e48-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![In Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="22e48-142">Bereitstellen der App in Azure</span><span class="sxs-lookup"><span data-stu-id="22e48-142">Deploy the app to Azure</span></span>

<span data-ttu-id="22e48-143">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen...** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="22e48-145">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="22e48-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="22e48-146">Wählen Sie **Microsoft Azure App Service** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="22e48-147">Klicken Sie auf das Zahnradsymbol, und wählen Sie anschließend **Profil erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="22e48-148">Klicken Sie auf **Profil erstellen**.</span><span class="sxs-lookup"><span data-stu-id="22e48-148">Select **Create Profile**.</span></span>

![Dialogfeld „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="22e48-150">Erstellen von Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="22e48-150">Create Azure resources</span></span>

<span data-ttu-id="22e48-151">Das Dialogfeld **App Service erstellen** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="22e48-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="22e48-152">Geben Sie Ihr Abonnement ein.</span><span class="sxs-lookup"><span data-stu-id="22e48-152">Enter your subscription.</span></span>
* <span data-ttu-id="22e48-153">Die Eingabefelder **App-Name**, **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="22e48-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="22e48-154">Sie können diese Namen beibehalten oder ändern.</span><span class="sxs-lookup"><span data-stu-id="22e48-154">You can keep these names or change them.</span></span>

![Dialogfeld „App Service“](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="22e48-156">Wählen Sie die Registerkarte **Dienste** aus, um eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="22e48-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="22e48-157">Klicken Sie auf das grüne Symbol **+**, um eine neue SQL-Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="22e48-157">Select the green **+** icon to create a new SQL Database</span></span>

![Neue SQL-Datenbank](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="22e48-159">Wählen Sie im Dialogfeld **SQL-Datenbank konfigurieren** die Option **Neu...** aus, um eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="22e48-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Neue SQL-Datenbank und -Server](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="22e48-161">Das Dialogfeld **SQL Server konfigurieren** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22e48-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="22e48-162">Geben Sie einen Administratorbenutzernamen und ein Kennwort ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="22e48-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="22e48-163">Sie können den standardmäßigen **Servernamen** beibehalten.</span><span class="sxs-lookup"><span data-stu-id="22e48-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="22e48-164">„admin“ ist als Benutzername des Administrators nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="22e48-164">"admin" isn't allowed as the administrator user name.</span></span>

![Dialogfeld „SQL Server konfigurieren“](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="22e48-166">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="22e48-166">Select **OK**.</span></span>

<span data-ttu-id="22e48-167">Visual Studio kehrt zum Dialogfeld **App Service erstellen** zurück.</span><span class="sxs-lookup"><span data-stu-id="22e48-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="22e48-168">Wählen Sie im Dialogfeld **App Service erstellen** die Option **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Dialogfeld „SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="22e48-170">Visual Studio erstellt die Web-App und SQL Server in Azure.</span><span class="sxs-lookup"><span data-stu-id="22e48-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="22e48-171">Dies kann einige Minuten in Anspruch nehmen.</span><span class="sxs-lookup"><span data-stu-id="22e48-171">This step can take a few minutes.</span></span> <span data-ttu-id="22e48-172">Weitere Informationen zu den erstellten Ressourcen finden Sie unter [Zusätzliche Ressourcen](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="22e48-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="22e48-173">Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Einstellungen**:</span><span class="sxs-lookup"><span data-stu-id="22e48-173">When deployment completes, select **Settings**:</span></span>

![Dialogfeld „SQL Server konfigurieren“](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="22e48-175">Gehen Sie im Bereich **Einstellungen** im Dialogfeld **Veröffentlichen** so vor:</span><span class="sxs-lookup"><span data-stu-id="22e48-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="22e48-176">Erweitern Sie **Datenbanken**, und aktivieren Sie **Diese Verbindungszeichenfolge zur Laufzeit verwenden**.</span><span class="sxs-lookup"><span data-stu-id="22e48-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="22e48-177">Erweitern Sie **Entity Framework-Migrationen**, und aktivieren Sie **Diese Migration auf Veröffentlichung anwenden**.</span><span class="sxs-lookup"><span data-stu-id="22e48-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="22e48-178">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="22e48-178">Select **Save**.</span></span> <span data-ttu-id="22e48-179">Visual Studio kehrt zum Dialogfeld **Veröffentlichen** zurück.</span><span class="sxs-lookup"><span data-stu-id="22e48-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Dialogfeld „Veröffentlichen“: Panel „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="22e48-181">Klicken Sie auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="22e48-181">Click **Publish**.</span></span> <span data-ttu-id="22e48-182">Visual Studio veröffentlicht Ihre App in Azure.</span><span class="sxs-lookup"><span data-stu-id="22e48-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="22e48-183">Wenn die Bereitstellung abgeschlossen ist, wird die App im Browser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="22e48-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="22e48-184">Testen der App in Azure</span><span class="sxs-lookup"><span data-stu-id="22e48-184">Test your app in Azure</span></span>

* <span data-ttu-id="22e48-185">Testen Sie die Links **About** und **Contact**.</span><span class="sxs-lookup"><span data-stu-id="22e48-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="22e48-186">Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="22e48-186">Register a new user</span></span>

![In Azure App Service in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="22e48-188">Aktualisieren der App</span><span class="sxs-lookup"><span data-stu-id="22e48-188">Update the app</span></span>

* <span data-ttu-id="22e48-189">Bearbeiten Sie die Razor Page *Pages/About.cshtml*, und verändern Sie deren Inhalte.</span><span class="sxs-lookup"><span data-stu-id="22e48-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="22e48-190">Sie können beispielsweise den Absatz so verändern, dass er „Hallo ASP.NET Core!“ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="22e48-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="22e48-191">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie erneut **Veröffentlichen...** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-191">Right-click on the project and select **Publish...** again.</span></span>

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="22e48-193">Nachdem die Anwendung veröffentlicht wurde, vergewissern Sie sich, dass die vorgenommenen Änderungen in Azure verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="22e48-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Der Überprüfungstask ist abgeschlossen.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="22e48-195">Bereinigen</span><span class="sxs-lookup"><span data-stu-id="22e48-195">Clean up</span></span>

<span data-ttu-id="22e48-196">Sobald Sie das Testen der App abgeschlossen haben, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und löschen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="22e48-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="22e48-197">Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="22e48-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="22e48-199">Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="22e48-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="22e48-201">Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="22e48-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="22e48-202">Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.</span><span class="sxs-lookup"><span data-stu-id="22e48-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="22e48-203">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="22e48-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="22e48-204">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="22e48-204">Additional resources</span></span>

* <span data-ttu-id="22e48-205">Visual Studio Code finden Sie unter [Veröffentlichungsprofile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="22e48-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="22e48-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="22e48-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="22e48-207">Azure-Ressourcengruppen</span><span class="sxs-lookup"><span data-stu-id="22e48-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="22e48-208">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="22e48-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:host-and-deploy/azure-apps/troubleshoot>
