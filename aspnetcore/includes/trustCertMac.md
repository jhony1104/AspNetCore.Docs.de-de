---
ms.openlocfilehash: 33772d3ad8bbb1ffc54792f8c31834849d0f9567
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57964153"
---
In Visual Studio für Mac wird ein Dialogfeld mit der folgenden Nachricht angezeigt:

*This project is configured to use SSL. To avoid SSL warnings in the browser you can choose to trust the self-signed certificate. Would you like to trust the IIS Express SSL certificate?* (Dieses Projekt ist für die Verwendung von SSL konfiguriert. Um SSL-Warnungen im Browser zu umgehen, können Sie sich dazu entscheiden, dem selbstsignierten Zertifikat zu vertrauen. Möchten Sie dem IIS Express-SLL-Zertifikat vertrauen?)

Wählen Sie **Ja** aus. Daraufhin wird das folgende Dialogfeld angezeigt:

![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).