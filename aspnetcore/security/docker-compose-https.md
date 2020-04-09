---
title: Hosten ASP.NET Core-Images im Container mithilfe von Docker-Komponieren mit HTTPS
author: ravipal
description: Erfahren Sie, wie Sie ASP.NET Core Images mit Docker Compose über HTTPS hosten
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381809"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="348cd-103">Hosten ASP.NET Core-Images mit Docker Compose über HTTPS</span><span class="sxs-lookup"><span data-stu-id="348cd-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="348cd-104">ASP.NET Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="348cd-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="348cd-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) basiert auf [Zertifikaten](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="348cd-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="348cd-106">In diesem Dokument wird erläutert, wie vorgefertigte Containerimages mit HTTPS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="348cd-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="348cd-107">Weitere Informationen finden Sie unter [Entwickeln ASP.NET Kernanwendungen mit Docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="348cd-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="348cd-108">In diesem Beispiel ist [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) oder höher des [Docker-Clients](https://www.docker.com/products/docker)erforderlich.</span><span class="sxs-lookup"><span data-stu-id="348cd-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="348cd-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="348cd-109">Prerequisites</span></span>

<span data-ttu-id="348cd-110">Das [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) oder höher ist für einige der Anweisungen in diesem Dokument erforderlich.</span><span class="sxs-lookup"><span data-stu-id="348cd-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="348cd-111">Zertifikate</span><span class="sxs-lookup"><span data-stu-id="348cd-111">Certificates</span></span>

<span data-ttu-id="348cd-112">Für das [Produktionshosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne ist ein Zertifikat einer [Zertifizierungsstelle](https://wikipedia.org/wiki/Certificate_authority) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="348cd-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="348cd-113">[Let's Encrypt](https://letsencrypt.org/)ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.</span><span class="sxs-lookup"><span data-stu-id="348cd-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="348cd-114">In diesem Dokument werden [selbstsignierte Entwicklungszertifikate](https://wikipedia.org/wiki/Self-signed_certificate) `localhost`zum Hosten vorinstallierter Images über verwendet.</span><span class="sxs-lookup"><span data-stu-id="348cd-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="348cd-115">Die Anweisungen ähneln der Verwendung von Produktionszertifikaten.</span><span class="sxs-lookup"><span data-stu-id="348cd-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="348cd-116">Für Produktionszertifikate:</span><span class="sxs-lookup"><span data-stu-id="348cd-116">For production certificates:</span></span>

* <span data-ttu-id="348cd-117">Das `dotnet dev-certs` Tool ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="348cd-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="348cd-118">Zertifikate müssen nicht an dem in den Anweisungen verwendeten Speicherort gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="348cd-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="348cd-119">Speichern Sie die Zertifikate an einem beliebigen Speicherort außerhalb des Standortverzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="348cd-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="348cd-120">Die Anweisungen im folgenden Abschnitt Volume mounten `volumes` Zertifikate in Container, die die Eigenschaft in *docker-compose.yml verwenden.*</span><span class="sxs-lookup"><span data-stu-id="348cd-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="348cd-121">Sie können Zertifikate zu Containerimages mit einem `COPY` Befehl in einer *Dockerfile*hinzufügen, es wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="348cd-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="348cd-122">Das Kopieren von Zertifikaten in ein Image wird aus den folgenden Gründen nicht empfohlen:</span><span class="sxs-lookup"><span data-stu-id="348cd-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="348cd-123">Es macht es schwierig, dasselbe Image für Tests mit Entwicklerzertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="348cd-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="348cd-124">Es macht es schwierig, dasselbe Image für Hosting mit Produktionszertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="348cd-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="348cd-125">Es besteht ein erhebliches Risiko der Offenlegung von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="348cd-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="348cd-126">Starten eines Containers mit https-Unterstützung mithilfe von docker compose</span><span class="sxs-lookup"><span data-stu-id="348cd-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="348cd-127">Verwenden Sie die folgenden Anweisungen für die Konfiguration Ihres Betriebssystems.</span><span class="sxs-lookup"><span data-stu-id="348cd-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="348cd-128">Windows mit Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="348cd-128">Windows using Linux containers</span></span>

<span data-ttu-id="348cd-129">Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:</span><span class="sxs-lookup"><span data-stu-id="348cd-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="348cd-130">Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="348cd-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="348cd-131">Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="348cd-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="348cd-132">Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="348cd-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="348cd-133">Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="348cd-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="348cd-134">macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="348cd-134">macOS or Linux</span></span>

<span data-ttu-id="348cd-135">Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:</span><span class="sxs-lookup"><span data-stu-id="348cd-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="348cd-136">`dotnet dev-certs https --trust`wird nur unter macOS und Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="348cd-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="348cd-137">Sie müssen Zertifikaten unter Linux in der Art und Weise vertrauen, die von Ihrer Distribution unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="348cd-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="348cd-138">Es ist wahrscheinlich, dass Sie dem Zertifikat in Ihrem Browser vertrauen müssen.</span><span class="sxs-lookup"><span data-stu-id="348cd-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="348cd-139">Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="348cd-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="348cd-140">Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="348cd-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="348cd-141">Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="348cd-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="348cd-142">Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="348cd-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="348cd-143">Windows mit Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="348cd-143">Windows using Windows containers</span></span>

<span data-ttu-id="348cd-144">Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:</span><span class="sxs-lookup"><span data-stu-id="348cd-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="348cd-145">Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="348cd-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="348cd-146">Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="348cd-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="348cd-147">Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="348cd-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="348cd-148">Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="348cd-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
