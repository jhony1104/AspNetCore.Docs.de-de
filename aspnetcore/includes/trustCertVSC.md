---
ms.openlocfilehash: a9bdff481b1a72a9ee19f4e51fada177530c0cbb
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472344"
---
*  Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:

    ```console
    dotnet dev-certs https --trust
    ```

    Über den vorherigen Befehl wird der folgende Dialog angezeigt:

    ![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

*    Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

     Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).