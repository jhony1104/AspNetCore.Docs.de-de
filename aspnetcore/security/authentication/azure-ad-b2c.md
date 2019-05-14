---
title: Cloudauthentifizierung mit Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Erfahren Sie, wie Azure Active Directory B2C-Authentifizierung mit ASP.NET Core einrichten.
ms.date: 02/27/2019
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 46654e38c780cfc9c44e7ba479419320ee684b76
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610683"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="3a97b-103">Cloudauthentifizierung mit Azure Active Directory B2C in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a97b-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="3a97b-104">Von [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="3a97b-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="3a97b-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloudlösung für die Verwaltung von Identität für Web- und mobilen apps.</span><span class="sxs-lookup"><span data-stu-id="3a97b-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="3a97b-106">Der Dienst ermöglicht die Authentifizierung für apps, die in der Cloud und lokal gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="3a97b-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="3a97b-107">Authentifizierungstypen sind einzelne Konten, Konten sozialer Netzwerke, und Verbundbenutzer Unternehmenskonten zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="3a97b-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="3a97b-108">Darüber hinaus bieten die Azure AD B2C Multi-Factor Authentication mit Minimalkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="3a97b-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="3a97b-109">Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote.</span><span class="sxs-lookup"><span data-stu-id="3a97b-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="3a97b-110">Azure AD-Mandant repräsentiert eine Organisation, auf, während ein Azure AD B2C-Mandanten ist, eine Sammlung von Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a97b-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="3a97b-111">Weitere Informationen finden Sie unter [Azure AD B2C: Häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="3a97b-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="3a97b-112">In diesem Tutorial erfahren Sie, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="3a97b-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a97b-113">Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="3a97b-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="3a97b-114">Registrieren einer app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3a97b-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="3a97b-115">Verwenden Sie Visual Studio zum Erstellen einer ASP.NET Core-Web-Apps so konfiguriert, dass die Azure AD B2C-Mandanten für die Authentifizierung verwenden</span><span class="sxs-lookup"><span data-stu-id="3a97b-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="3a97b-116">Konfigurieren von Richtlinien, die Steuerung des Verhaltens des Azure AD B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="3a97b-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a97b-117">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3a97b-117">Prerequisites</span></span>

<span data-ttu-id="3a97b-118">Die folgenden Voraussetzungen gelten für diese exemplarische Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="3a97b-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="3a97b-119">Microsoft Azure-Abonnement</span><span class="sxs-lookup"><span data-stu-id="3a97b-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="3a97b-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="3a97b-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="3a97b-121">Erstellen der Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="3a97b-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="3a97b-122">Erstellen eines Azure Active Directory B2C-Mandanten [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="3a97b-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="3a97b-123">Wenn Sie aufgefordert werden, ist ein Azure-Abonnement des Mandanten zuordnen optional für dieses Tutorial.</span><span class="sxs-lookup"><span data-stu-id="3a97b-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="3a97b-124">Registrieren der app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3a97b-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="3a97b-125">In den neu erstellten Azure AD B2C-Mandanten Registrieren Ihrer app über [die Schritte in der Dokumentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) unter der **Registrieren einer WebApp** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="3a97b-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) under the **Register a web app** section.</span></span> <span data-ttu-id="3a97b-126">Beenden der **erstellen Sie ein Web-app-clientgeheimnis** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="3a97b-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="3a97b-127">Ein clientgeheimnis ist für dieses Tutorial nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3a97b-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="3a97b-128">Verwenden Sie die folgenden Werte ein:</span><span class="sxs-lookup"><span data-stu-id="3a97b-128">Use the following values:</span></span>

| <span data-ttu-id="3a97b-129">Einstellung</span><span class="sxs-lookup"><span data-stu-id="3a97b-129">Setting</span></span>                       | <span data-ttu-id="3a97b-130">Wert</span><span class="sxs-lookup"><span data-stu-id="3a97b-130">Value</span></span>                     | <span data-ttu-id="3a97b-131">Hinweise</span><span class="sxs-lookup"><span data-stu-id="3a97b-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a97b-132">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a97b-132">**Name**</span></span>                      | <span data-ttu-id="3a97b-133">*&lt;App-name&gt;*</span><span class="sxs-lookup"><span data-stu-id="3a97b-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="3a97b-134">Geben Sie einen **Namen** für die app, die Ihre app für Kunden beschreibt.</span><span class="sxs-lookup"><span data-stu-id="3a97b-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="3a97b-135">**Einschließen von Web-app / web-API**</span><span class="sxs-lookup"><span data-stu-id="3a97b-135">**Include web app / web API**</span></span> | <span data-ttu-id="3a97b-136">Ja</span><span class="sxs-lookup"><span data-stu-id="3a97b-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="3a97b-137">**Impliziten Fluss zulassen**</span><span class="sxs-lookup"><span data-stu-id="3a97b-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="3a97b-138">Ja</span><span class="sxs-lookup"><span data-stu-id="3a97b-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="3a97b-139">**Antwort-URL**</span><span class="sxs-lookup"><span data-stu-id="3a97b-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="3a97b-140">Antwort-URLs sind Endpunkte, an denen Azure AD B2C, die von Ihrer app angeforderte Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="3a97b-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="3a97b-141">Visual Studio bietet die Antwort-URL verwenden.</span><span class="sxs-lookup"><span data-stu-id="3a97b-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="3a97b-142">Geben Sie für den Moment `https://localhost:44300/signin-oidc` um das Formular auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="3a97b-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="3a97b-143">**App-ID-URI**</span><span class="sxs-lookup"><span data-stu-id="3a97b-143">**App ID URI**</span></span>                | <span data-ttu-id="3a97b-144">Leer lassen</span><span class="sxs-lookup"><span data-stu-id="3a97b-144">Leave blank</span></span>               | <span data-ttu-id="3a97b-145">Für dieses Tutorial erforderlich nicht.</span><span class="sxs-lookup"><span data-stu-id="3a97b-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="3a97b-146">**Nativen Client einschließen**</span><span class="sxs-lookup"><span data-stu-id="3a97b-146">**Include native client**</span></span>     | <span data-ttu-id="3a97b-147">Nein</span><span class="sxs-lookup"><span data-stu-id="3a97b-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="3a97b-148">Wenn das Einrichten einer nicht-Localhost-Antwort-URL, berücksichtigen die [Einschränkungen auf, was in der Antwort-URL-Liste zulässig ist](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span><span class="sxs-lookup"><span data-stu-id="3a97b-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span></span> 

<span data-ttu-id="3a97b-149">Nachdem die app registriert wurde, wird die Liste der apps im Mandanten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3a97b-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="3a97b-150">Wählen Sie die app, die gerade registriert wurde.</span><span class="sxs-lookup"><span data-stu-id="3a97b-150">Select the app that was just registered.</span></span> <span data-ttu-id="3a97b-151">Wählen Sie die **Kopie** Symbol rechts neben der **Anwendungs-ID** Feld, um ihn in die Zwischenablage zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="3a97b-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="3a97b-152">Nichts mehr zu diesem Zeitpunkt im Azure AD B2C-Mandanten konfiguriert werden können, aber lassen das Browserfenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="3a97b-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="3a97b-153">Es gibt weitere Konfigurationsschritte nach der Erstellung der ASP.NET Core-app.</span><span class="sxs-lookup"><span data-stu-id="3a97b-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="3a97b-154">Erstellen einer ASP.NET Core-Apps in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a97b-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="3a97b-155">Die Visual Studio Web Application-Vorlage kann für die verwenden Sie des Azure AD B2C-Mandantenverwaltungs für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3a97b-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="3a97b-156">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3a97b-156">In Visual Studio:</span></span>

1. <span data-ttu-id="3a97b-157">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="3a97b-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="3a97b-158">Wählen Sie **Webanwendung** aus der Liste der Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="3a97b-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="3a97b-159">Wählen Sie die **Authentifizierung ändern** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="3a97b-159">Select the **Change Authentication** button.</span></span>
    
    ![Schaltfläche "Authentifizierung ändern"](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="3a97b-161">In der **Authentifizierung ändern** wählen Sie im Dialogfeld **einzelne Benutzerkonten**, und wählen Sie dann **Herstellen einer Verbindung mit einem vorhandenen Benutzerspeicher in der Cloud** in der Dropdownliste aus.</span><span class="sxs-lookup"><span data-stu-id="3a97b-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Authentifizierung-Dialogfeld "ändern"](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="3a97b-163">Füllen Sie das Formular mit den folgenden Werten:</span><span class="sxs-lookup"><span data-stu-id="3a97b-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="3a97b-164">Einstellung</span><span class="sxs-lookup"><span data-stu-id="3a97b-164">Setting</span></span>                       | <span data-ttu-id="3a97b-165">Wert</span><span class="sxs-lookup"><span data-stu-id="3a97b-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="3a97b-166">**Domänenname**</span><span class="sxs-lookup"><span data-stu-id="3a97b-166">**Domain Name**</span></span>               | <span data-ttu-id="3a97b-167">*&lt;der Domänenname Ihres B2C-Mandanten&gt;*</span><span class="sxs-lookup"><span data-stu-id="3a97b-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="3a97b-168">**Anwendungs-ID**</span><span class="sxs-lookup"><span data-stu-id="3a97b-168">**Application ID**</span></span>            | <span data-ttu-id="3a97b-169">*&lt;Fügen Sie die Anwendungs-ID aus der Zwischenablage&gt;*</span><span class="sxs-lookup"><span data-stu-id="3a97b-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="3a97b-170">**Rückrufpfad**</span><span class="sxs-lookup"><span data-stu-id="3a97b-170">**Callback Path**</span></span>             | <span data-ttu-id="3a97b-171">*&lt;Verwenden Sie den Standardwert&gt;*</span><span class="sxs-lookup"><span data-stu-id="3a97b-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="3a97b-172">**Richtlinie für Registrierung oder Anmeldung**</span><span class="sxs-lookup"><span data-stu-id="3a97b-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="3a97b-173">**Richtlinie für die kennwortzurücksetzung**</span><span class="sxs-lookup"><span data-stu-id="3a97b-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="3a97b-174">**Richtlinie bearbeiten**</span><span class="sxs-lookup"><span data-stu-id="3a97b-174">**Edit profile policy**</span></span>       | <span data-ttu-id="3a97b-175">*&lt;Leer lassen&gt;*</span><span class="sxs-lookup"><span data-stu-id="3a97b-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="3a97b-176">Wählen Sie die **Kopie** link **Antwort-URI** der Antwort-URI in die Zwischenablage zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="3a97b-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="3a97b-177">Wählen Sie **OK** schließen die **Authentifizierung ändern** Dialogfeld.</span><span class="sxs-lookup"><span data-stu-id="3a97b-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="3a97b-178">Wählen Sie **OK** zum Erstellen der Web-app.</span><span class="sxs-lookup"><span data-stu-id="3a97b-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="3a97b-179">Beenden Sie die B2C-app-Registrierung</span><span class="sxs-lookup"><span data-stu-id="3a97b-179">Finish the B2C app registration</span></span>

<span data-ttu-id="3a97b-180">Zurück zum Browserfenster mit den B2C-app-Eigenschaften noch geöffnet.</span><span class="sxs-lookup"><span data-stu-id="3a97b-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="3a97b-181">Ändern Sie den temporären **Antwort-URL** früher auf den Wert kopiert aus Visual Studio angegeben.</span><span class="sxs-lookup"><span data-stu-id="3a97b-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="3a97b-182">Wählen Sie **speichern** am oberen Rand des Fensters.</span><span class="sxs-lookup"><span data-stu-id="3a97b-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="3a97b-183">Wenn Sie nicht die Antwort-URL kopieren, verwenden Sie die HTTPS-Adresse aus der Registerkarte "Debuggen" in den Web-Projekteigenschaften, und fügen die **CallbackPath** Wert *"appSettings.JSON"*.</span><span class="sxs-lookup"><span data-stu-id="3a97b-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="3a97b-184">Konfigurieren von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="3a97b-184">Configure policies</span></span>

<span data-ttu-id="3a97b-185">Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, [erstellen Sie eine Richtlinie für Registrierung oder Anmeldung](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), und klicken Sie dann [erstellen Sie eine Richtlinie zur kennwortzurücksetzung](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span><span class="sxs-lookup"><span data-stu-id="3a97b-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span></span> <span data-ttu-id="3a97b-186">Verwenden Sie die Beispielwerte finden Sie in der Dokumentation für **Identitätsanbieter**, **Registrierungsattribute**, und **anwendungsansprüche**.</span><span class="sxs-lookup"><span data-stu-id="3a97b-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="3a97b-187">Mithilfe der **jetzt ausführen** , um die Richtlinien zu testen, wie in der Dokumentation beschrieben ist optional.</span><span class="sxs-lookup"><span data-stu-id="3a97b-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="3a97b-188">Stellen Sie sicher, Richtliniennamen werden genau wie in der Dokumentation beschrieben, wie diese Richtlinien, in verwendet wurden der **Authentifizierung ändern** Dialogfeld in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a97b-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="3a97b-189">Namen der Richtlinien überprüft werden können, im *"appSettings.JSON"*.</span><span class="sxs-lookup"><span data-stu-id="3a97b-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="3a97b-190">Konfigurieren Sie die zugrunde liegenden OpenIdConnectOptions/JwtBearer/Cookie-Optionen</span><span class="sxs-lookup"><span data-stu-id="3a97b-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="3a97b-191">Um die zugrunde liegenden Optionen direkt konfigurieren zu können, verwenden Sie die entsprechenden Schema-Konstante in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3a97b-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="3a97b-192">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="3a97b-192">Run the app</span></span>

<span data-ttu-id="3a97b-193">Drücken Sie in Visual Studio **F5** erstellen und Ausführen der app.</span><span class="sxs-lookup"><span data-stu-id="3a97b-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="3a97b-194">Nachdem die Web-app gestartet wird, wählen Sie **Accept** akzeptieren die Verwendung von Cookies (ggf.), und wählen Sie dann **Anmeldung**.</span><span class="sxs-lookup"><span data-stu-id="3a97b-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Melden Sie sich bei der app](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="3a97b-196">Der Browser leitet an die Azure AD B2C-Mandanten.</span><span class="sxs-lookup"><span data-stu-id="3a97b-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="3a97b-197">Melden Sie sich mit einem vorhandenen Konto, (wenn es erstellt wurde, testen die Richtlinien), oder wählen Sie **registrieren Sie sich jetzt** um ein neues Konto zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3a97b-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="3a97b-198">Die **haben Ihr Kennwort vergessen?** Link wird verwendet, um ein vergessenes Kennwort zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="3a97b-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C-Anmeldung](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="3a97b-200">Nach erfolgreicher Anmeldung, leitet den Browser zur Web-app.</span><span class="sxs-lookup"><span data-stu-id="3a97b-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Erfolgreich](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="3a97b-202">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3a97b-202">Next steps</span></span>

<span data-ttu-id="3a97b-203">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="3a97b-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a97b-204">Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="3a97b-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="3a97b-205">Registrieren einer app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="3a97b-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="3a97b-206">Verwenden Sie Visual Studio zum Erstellen einer ASP.NET Core-Webanwendung so konfiguriert, dass die Azure AD B2C-Mandanten für die Authentifizierung verwenden</span><span class="sxs-lookup"><span data-stu-id="3a97b-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="3a97b-207">Konfigurieren von Richtlinien, die Steuerung des Verhaltens des Azure AD B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="3a97b-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="3a97b-208">Die ASP.NET Core-app ist jetzt konfiguriert, um die Azure AD B2C für die Authentifizierung verwenden die [Authorize-Attribut](xref:security/authorization/simple) kann zum Schutz Ihrer app verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a97b-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="3a97b-209">Fahren Sie fort, entwickeln Ihre app, indem Sie auf:</span><span class="sxs-lookup"><span data-stu-id="3a97b-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="3a97b-210">[Anpassen der Benutzeroberfläche von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="3a97b-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="3a97b-211">[Konfigurieren Sie die Anforderungen an die Kennwortkomplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="3a97b-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="3a97b-212">[Aktivieren von Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="3a97b-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="3a97b-213">Konfigurieren zusätzlicher Identitätsanbieter, z. B. [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), und andere.</span><span class="sxs-lookup"><span data-stu-id="3a97b-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="3a97b-214">[Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) um zusätzliche Benutzerinformationen zu erhalten, wie z. B. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3a97b-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="3a97b-215">[Sichern einer ASP.NET Core-Web-API mit Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span><span class="sxs-lookup"><span data-stu-id="3a97b-215">[Secure an ASP.NET Core web API using Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span></span>
* <span data-ttu-id="3a97b-216">[Rufen Sie eine .NET Web-API aus einer .NET Web-app mithilfe von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3a97b-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>
