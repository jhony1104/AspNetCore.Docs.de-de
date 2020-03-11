---
title: Hosting von ASP.net Core Images mit docker über HTTPS
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: 2f338e8883ca926c0f9a7ab339f58b088151cc87
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653827"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Hosting von ASP.net Core Images mit docker über HTTPS

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.net Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl). [Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.

In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.

Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.

Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://www.microsoft.com/net/download) oder höher erforderlich.

## <a name="certificates"></a>Zertifikate

Ein Zertifikat von einer [Zertifizierungs](https://wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich. [Let's Encrypt](https://letsencrypt.org/) ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.

In diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://en.wikipedia.org/wiki/Self-signed_certificate) zum Hosting von vorgefertigten Images über `localhost`verwendet. Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.

Für produktionscerts:

* Das `dotnet dev-certs` Tool ist nicht erforderlich.
* Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird. Jeder Speicherort sollte funktionieren, obwohl das Speichern von Zertifikaten innerhalb Ihres Website Verzeichnisses nicht empfohlen wird.

Die Anweisungen im folgenden Abschnitt enthalten Informationen zum Einbinden von Zertifikaten in Container mithilfe der `-v` Befehlszeilenoption von Docker. Sie können Zertifikate in Container Images mit einem `COPY`-Befehl in einer *dockerfile-Datei*hinzufügen, dies wird jedoch nicht empfohlen. Das Kopieren von Zertifikaten in ein Abbild wird aus den folgenden Gründen nicht empfohlen:

* Es ist schwierig, das gleiche Image für Tests mit Entwickler Zertifikaten zu verwenden.
* Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.
* Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.

## <a name="running-pre-built-container-images-with-https"></a>Ausführen von vorgefertigten Container Images mit HTTPS

Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.

### <a name="windows-using-linux-containers"></a>Windows mithilfe von Linux-Containern

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie `{ password here }` in den vorangehenden Befehlen durch ein Kennwort.

Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.

### <a name="macos-or-linux"></a>macOS oder Linux

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` wird nur unter macOS und Windows unterstützt. Sie müssen Zertifikate unter Linux in der von Ihrer Distribution unterstützten Weise als vertrauenswürdig einstufen. Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.

Ersetzen Sie `{ password here }` in den vorangehenden Befehlen durch ein Kennwort.

Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.

### <a name="windows-using-windows-containers"></a>Windows mithilfe von Windows-Containern

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie `{ password here }` in den vorangehenden Befehlen durch ein Kennwort.

Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.
