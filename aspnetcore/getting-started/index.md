---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: Kurztutorial, in dem eine einfache Hello World-App mit ASP.NET Core erstellt und ausgeführt wird.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
ms.openlocfilehash: eee927f4306fa7757f3f361e6c6f367658512897
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341809"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="7e980-103">Tutorial: Erste Schritte mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e980-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="7e980-104">Dieses Tutorial zeigt, wie die .NET Core-Befehlszeilenschnittstelle verwendet wird, um eine ASP.NET Core-Web-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e980-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="7e980-105">Sie lernen, die folgende Aufgaben auszuführen:</span><span class="sxs-lookup"><span data-stu-id="7e980-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e980-106">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="7e980-106">Create a web app project.</span></span>
> * <span data-ttu-id="7e980-107">Aktivieren Sie lokales HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7e980-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="7e980-108">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="7e980-108">Run the app.</span></span>
> * <span data-ttu-id="7e980-109">Bearbeiten Sie eine Razor-Seite.</span><span class="sxs-lookup"><span data-stu-id="7e980-109">Edit a Razor page.</span></span>

<span data-ttu-id="7e980-110">Am Schluss werden Sie eine funktionierende Web-App auf Ihrem lokalen Computer besitzen.</span><span class="sxs-lookup"><span data-stu-id="7e980-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web-App-Startseite](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="7e980-112">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="7e980-112">Prerequisites</span></span>

* [<span data-ttu-id="7e980-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="7e980-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="7e980-114">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="7e980-114">Create a web app project</span></span>

<span data-ttu-id="7e980-115">Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="7e980-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="7e980-116">Aktivieren Sie lokales HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7e980-116">Enable local HTTPS</span></span>

<span data-ttu-id="7e980-117">Vertrauen Sie dem HTTPS-Entwicklungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="7e980-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="7e980-118">Windows</span><span class="sxs-lookup"><span data-stu-id="7e980-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="7e980-119">Über den vorherigen Befehl wird der folgende Dialog angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7e980-119">The preceding command displays the following dialog:</span></span>

![Dialogfeld „Sicherheitswarnung“](_static/cert.png)

<span data-ttu-id="7e980-121">Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="7e980-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="7e980-122">macOS</span><span class="sxs-lookup"><span data-stu-id="7e980-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="7e980-123">Über den vorherigen Befehl wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7e980-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="7e980-124">*Trusting the HTTPS development certificate was requested. Wenn das Zertifikat nicht bereits vertrauenswürdig, ist führen wir den folgenden Befehl aus:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="7e980-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>  
<span data-ttu-id="7e980-125">\*Dieser Befehl fordert Sie möglicherweise zur Eingabe Ihres Kennworts auf, um das Zertifikat für die Systemkeychain zu installieren.</span><span class="sxs-lookup"><span data-stu-id="7e980-125">\*This command might prompt you for your password to install the certificate on the system keychain.</span></span>

<span data-ttu-id="7e980-126">Kennwort:\*</span><span class="sxs-lookup"><span data-stu-id="7e980-126">Password:\*</span></span>

<span data-ttu-id="7e980-127">Geben Sie Ihr Kennwort ein, wenn Sie die Vertrauenswürdigkeit des Entwicklungszertifikats bestätigen möchten.</span><span class="sxs-lookup"><span data-stu-id="7e980-127">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="7e980-128">Linux</span><span class="sxs-lookup"><span data-stu-id="7e980-128">Linux</span></span>](#tab/linux)

<span data-ttu-id="7e980-129">Weitere Informationen zum Bestätigen der Vertrauenswürdigkeit eines HTTPS-Entwicklungszertifikats finden Sie in der Dokumentation zu Ihrer Linux-Distribution.</span><span class="sxs-lookup"><span data-stu-id="7e980-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

## <a name="run-the-app"></a><span data-ttu-id="7e980-130">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="7e980-130">Run the app</span></span>

<span data-ttu-id="7e980-131">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="7e980-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="7e980-132">Nachdem die Befehlsshell angibt, dass die App gestartet wurde, wechseln Sie zu [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="7e980-132">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="7e980-133">Klicken Sie auf **Accept** (Akzeptieren), um die Datenschutz- und Cookierichtlinie zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="7e980-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="7e980-134">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="7e980-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="7e980-135">Bearbeiten einer Razor-Seite</span><span class="sxs-lookup"><span data-stu-id="7e980-135">Edit a Razor page</span></span>

<span data-ttu-id="7e980-136">Öffnen Sie *Pages/Index.cshtml*, und ändern Sie die Seite mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="7e980-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="7e980-137">Navigieren Sie zu [https://localhost:5001](https://localhost:5001), und überprüfen Sie, ob die Änderungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="7e980-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7e980-138">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="7e980-138">Next steps</span></span>

<span data-ttu-id="7e980-139">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="7e980-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e980-140">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="7e980-140">Create a web app project.</span></span>
> * <span data-ttu-id="7e980-141">Aktivieren Sie lokales HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7e980-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="7e980-142">Führen Sie das Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="7e980-142">Run the project.</span></span>
> * <span data-ttu-id="7e980-143">Führen Sie eine Änderung durch.</span><span class="sxs-lookup"><span data-stu-id="7e980-143">Make a change.</span></span>

<span data-ttu-id="7e980-144">Um mehr über ASP.NET Core zu lernen, lesen Sie die Einleitung:</span><span class="sxs-lookup"><span data-stu-id="7e980-144">To learn more about ASP.NET Core, see the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index>
