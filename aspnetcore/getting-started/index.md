---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: Ein kurzes Tutorial, in dem eine einfache Hallo Welt-App mit ASP.NET Core erstellt und ausgeführt wird.
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081127"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="757ed-103">Tutorial: Erste Schritte mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="757ed-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="757ed-104">Dieses Tutorial zeigt, wie die .NET Core-Befehlszeilenschnittstelle verwendet wird, um eine ASP.NET Core-Web-App zu erstellen und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="757ed-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="757ed-105">Sie lernen, die folgende Aufgaben auszuführen:</span><span class="sxs-lookup"><span data-stu-id="757ed-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="757ed-106">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="757ed-106">Create a web app project.</span></span>
> * <span data-ttu-id="757ed-107">Vertrauen Sie dem Entwicklungszertifikat.</span><span class="sxs-lookup"><span data-stu-id="757ed-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="757ed-108">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="757ed-108">Run the app.</span></span>
> * <span data-ttu-id="757ed-109">Bearbeiten Sie eine Razor-Seite.</span><span class="sxs-lookup"><span data-stu-id="757ed-109">Edit a Razor page.</span></span>

<span data-ttu-id="757ed-110">Am Schluss werden Sie eine funktionierende Web-App auf Ihrem lokalen Computer besitzen.</span><span class="sxs-lookup"><span data-stu-id="757ed-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web-App-Startseite](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="757ed-112">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="757ed-112">Prerequisites</span></span>

* [<span data-ttu-id="757ed-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="757ed-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="757ed-114">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="757ed-114">Create a web app project</span></span>

<span data-ttu-id="757ed-115">Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="757ed-115">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="757ed-116">Dem Entwicklungszertifikat vertrauen</span><span class="sxs-lookup"><span data-stu-id="757ed-116">Trust the development certificate</span></span>

<span data-ttu-id="757ed-117">Vertrauen Sie dem HTTPS-Entwicklungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="757ed-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="757ed-118">Windows</span><span class="sxs-lookup"><span data-stu-id="757ed-118">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="757ed-119">Über den vorherigen Befehl wird der folgende Dialog angezeigt:</span><span class="sxs-lookup"><span data-stu-id="757ed-119">The preceding command displays the following dialog:</span></span>

![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

<span data-ttu-id="757ed-121">Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="757ed-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="757ed-122">macOS</span><span class="sxs-lookup"><span data-stu-id="757ed-122">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="757ed-123">Über den vorherigen Befehl wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="757ed-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="757ed-124">*Trusting the HTTPS development certificate was requested. Wenn das Zertifikat nicht bereits vertrauenswürdig, ist führen wir den folgenden Befehl aus:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="757ed-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="757ed-125">Dieser Befehl fordert Sie möglicherweise zur Eingabe Ihres Kennworts auf, um das Zertifikat in der Keychain für das System zu installieren.</span><span class="sxs-lookup"><span data-stu-id="757ed-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="757ed-126">Geben Sie Ihr Kennwort ein, wenn Sie die Vertrauenswürdigkeit des Entwicklungszertifikats bestätigen möchten.</span><span class="sxs-lookup"><span data-stu-id="757ed-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="757ed-127">Linux</span><span class="sxs-lookup"><span data-stu-id="757ed-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="757ed-128">Informationen zum Windows-Subsystem für Linux finden Sie unter [Dem HTTPS-Zertifikat des Windows-Subsystems für Linux vertrauen](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="757ed-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="757ed-129">Weitere Informationen zum Bestätigen der Vertrauenswürdigkeit eines HTTPS-Entwicklungszertifikats finden Sie in der Dokumentation zu Ihrer Linux-Distribution.</span><span class="sxs-lookup"><span data-stu-id="757ed-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="757ed-130">Weitere Informationen finden Sie unter [Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikat als vertrauenswürdig](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="757ed-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="757ed-131">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="757ed-131">Run the app</span></span>

<span data-ttu-id="757ed-132">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="757ed-132">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="757ed-133">Nachdem die Befehlsshell angibt, dass die App gestartet wurde, wechseln Sie zu [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="757ed-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="757ed-134">Klicken Sie auf **Accept** (Akzeptieren), um die Datenschutz- und Cookierichtlinie zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="757ed-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="757ed-135">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="757ed-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="757ed-136">Bearbeiten einer Razor-Seite</span><span class="sxs-lookup"><span data-stu-id="757ed-136">Edit a Razor page</span></span>

<span data-ttu-id="757ed-137">Öffnen Sie *Pages/Index.cshtml*, und ändern Sie die Seite mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="757ed-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="757ed-138">Navigieren Sie zu [https://localhost:5001](https://localhost:5001), und überprüfen Sie, ob die Änderungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="757ed-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="757ed-139">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="757ed-139">Next steps</span></span>

<span data-ttu-id="757ed-140">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="757ed-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="757ed-141">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="757ed-141">Create a web app project.</span></span>
> * <span data-ttu-id="757ed-142">Vertrauen Sie dem Entwicklungszertifikat.</span><span class="sxs-lookup"><span data-stu-id="757ed-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="757ed-143">Führen Sie das Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="757ed-143">Run the project.</span></span>
> * <span data-ttu-id="757ed-144">Führen Sie eine Änderung durch.</span><span class="sxs-lookup"><span data-stu-id="757ed-144">Make a change.</span></span>

<span data-ttu-id="757ed-145">Um mehr über ASP.NET Core zu lernen, machen Sie sich mit dem empfohlenen Lernpfad in der Einführung vertraut:</span><span class="sxs-lookup"><span data-stu-id="757ed-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
