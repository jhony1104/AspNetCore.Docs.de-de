---
title: Hosting von ASP.net Core Images mit docker über HTTPS
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: security/docker-https
ms.openlocfilehash: f17a3abe1b00b39b7b6787be5b20ce65771190b8
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619695"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="595a9-103">Hosting von ASP.net Core Images mit docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="595a9-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="595a9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="595a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="595a9-105">ASP.net Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="595a9-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="595a9-106">[Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="595a9-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="595a9-107">In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="595a9-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="595a9-108">Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) für Entwicklungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="595a9-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="595a9-109">Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.</span><span class="sxs-lookup"><span data-stu-id="595a9-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="595a9-110">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="595a9-110">Prerequisites</span></span>

<span data-ttu-id="595a9-111">Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://www.microsoft.com/net/download) oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="595a9-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="595a9-112">Zertifikate</span><span class="sxs-lookup"><span data-stu-id="595a9-112">Certificates</span></span>

<span data-ttu-id="595a9-113">Ein Zertifikat von einer [Zertifizierungs](https://en.wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich.</span><span class="sxs-lookup"><span data-stu-id="595a9-113">A certificate from a [certificate authority](https://en.wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span>  <span data-ttu-id="595a9-114">[Wir verschlüsseln](https://letsencrypt.org/) eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.</span><span class="sxs-lookup"><span data-stu-id="595a9-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="595a9-115">In`localhost`diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://en.wikipedia.org/wiki/Self-signed_certificate) für das Hosting von vordefinierten Images verwendet.</span><span class="sxs-lookup"><span data-stu-id="595a9-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="595a9-116">Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="595a9-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="595a9-117">Für produktionscerts:</span><span class="sxs-lookup"><span data-stu-id="595a9-117">For production certs:</span></span>

* <span data-ttu-id="595a9-118">Das `dotnet dev-certs` Tool ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="595a9-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="595a9-119">Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="595a9-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="595a9-120">Jeder Speicherort sollte funktionieren, obwohl das Speichern von Zertifikaten innerhalb Ihres Website Verzeichnisses nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="595a9-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="595a9-121">Die Anweisungen Volume einbinden von Zertifikaten in Containern.</span><span class="sxs-lookup"><span data-stu-id="595a9-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="595a9-122">Sie können Zertifikate in Container Images mit einem `COPY` Befehl in einer dockerfile-Datei hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="595a9-122">You can add certificates into container images with a `COPY` command in a Dockerfile.</span></span> <span data-ttu-id="595a9-123">Das Kopieren von Zertifikaten in ein Abbild ist nicht empfehlenswert:</span><span class="sxs-lookup"><span data-stu-id="595a9-123">Copying certificates into an image is not recommended:</span></span>

* <span data-ttu-id="595a9-124">Es ist schwierig, das gleiche Image für Tests mit Entwickler Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="595a9-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="595a9-125">Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="595a9-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="595a9-126">Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="595a9-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="595a9-127">Ausführen von vorgefertigten Container Images mit HTTPS</span><span class="sxs-lookup"><span data-stu-id="595a9-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="595a9-128">Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="595a9-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="595a9-129">Windows mithilfe von Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="595a9-129">Windows using Linux containers</span></span>

<span data-ttu-id="595a9-130">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="595a9-130">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="595a9-131">Ersetzen `{ password here }` Sie in den vorangehenden Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="595a9-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="595a9-132">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="595a9-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="595a9-133">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="595a9-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="595a9-134">macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="595a9-134">macOS or Linux</span></span>

<span data-ttu-id="595a9-135">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="595a9-135">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="595a9-136">`dotnet dev-certs https --trust`wird nur unter macOS und Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="595a9-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="595a9-137">Sie müssen Zertifikate unter Linux in der von Ihrer Distribution unterstützten Weise als vertrauenswürdig einstufen.</span><span class="sxs-lookup"><span data-stu-id="595a9-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="595a9-138">Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.</span><span class="sxs-lookup"><span data-stu-id="595a9-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="595a9-139">Ersetzen `{ password here }` Sie in den vorangehenden Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="595a9-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="595a9-140">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="595a9-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="595a9-141">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="595a9-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="595a9-142">Windows mithilfe von Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="595a9-142">Windows using Windows containers</span></span>

<span data-ttu-id="595a9-143">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="595a9-143">Generate certificate and configure local machine:</span></span>

```console
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="595a9-144">Ersetzen `{ password here }` Sie in den vorangehenden Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="595a9-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="595a9-145">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="595a9-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="595a9-146">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="595a9-146">The password must match the password used for the certificate.</span></span>