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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hosten ASP.NET Core-Images mit Docker Compose über HTTPS


ASP.NET Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl). [HTTPS](https://en.wikipedia.org/wiki/HTTPS) basiert auf [Zertifikaten](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.

In diesem Dokument wird erläutert, wie vorgefertigte Containerimages mit HTTPS ausgeführt werden.

Weitere Informationen finden Sie unter [Entwickeln ASP.NET Kernanwendungen mit Docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.

In diesem Beispiel ist [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) oder höher des [Docker-Clients](https://www.docker.com/products/docker)erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

Das [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) oder höher ist für einige der Anweisungen in diesem Dokument erforderlich.

## <a name="certificates"></a>Zertifikate

Für das [Produktionshosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne ist ein Zertifikat einer [Zertifizierungsstelle](https://wikipedia.org/wiki/Certificate_authority) erforderlich. [Let's Encrypt](https://letsencrypt.org/)ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.

In diesem Dokument werden [selbstsignierte Entwicklungszertifikate](https://wikipedia.org/wiki/Self-signed_certificate) `localhost`zum Hosten vorinstallierter Images über verwendet. Die Anweisungen ähneln der Verwendung von Produktionszertifikaten.

Für Produktionszertifikate:

* Das `dotnet dev-certs` Tool ist nicht erforderlich.
* Zertifikate müssen nicht an dem in den Anweisungen verwendeten Speicherort gespeichert werden. Speichern Sie die Zertifikate an einem beliebigen Speicherort außerhalb des Standortverzeichnisses.

Die Anweisungen im folgenden Abschnitt Volume mounten `volumes` Zertifikate in Container, die die Eigenschaft in *docker-compose.yml verwenden.* Sie können Zertifikate zu Containerimages mit einem `COPY` Befehl in einer *Dockerfile*hinzufügen, es wird jedoch nicht empfohlen. Das Kopieren von Zertifikaten in ein Image wird aus den folgenden Gründen nicht empfohlen:

* Es macht es schwierig, dasselbe Image für Tests mit Entwicklerzertifikaten zu verwenden.
* Es macht es schwierig, dasselbe Image für Hosting mit Produktionszertifikaten zu verwenden.
* Es besteht ein erhebliches Risiko der Offenlegung von Zertifikaten.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Starten eines Containers mit https-Unterstützung mithilfe von docker compose

Verwenden Sie die folgenden Anweisungen für die Konfiguration Ihres Betriebssystems.

### <a name="windows-using-linux-containers"></a>Windows mit Linux-Containern

Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.

Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:

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
Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.

Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS oder Linux

Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`wird nur unter macOS und Windows unterstützt. Sie müssen Zertifikaten unter Linux in der Art und Weise vertrauen, die von Ihrer Distribution unterstützt wird. Es ist wahrscheinlich, dass Sie dem Zertifikat in Ihrem Browser vertrauen müssen.

Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.

Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:

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
Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.

Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows mit Windows-Containern

Generieren Sie ein Zertifikat, und konfigurieren Sie den lokalen Computer:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie in `{ password here }` den vorherigen Befehlen durch ein Kennwort.

Erstellen Sie eine _docker-compose.debug.yml-Datei_ mit dem folgenden Inhalt:

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
Das in der Docker-Verfassen-Datei angegebene Kennwort muss mit dem für das Zertifikat verwendeten Kennwort übereinstimmen.

Starten Sie den Container mit ASP.NET Core für HTTPS konfiguriert:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
