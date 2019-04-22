---
ms.openlocfilehash: 260f774fdba4d16a4fcb00ac1c699acf4d1bf5b5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615382"
---
* Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:

  ```console
  dotnet dev-certs https --trust
  ```

  Über den vorherigen Befehl wird der folgende Dialog angezeigt:

  ![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

* Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

  Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
