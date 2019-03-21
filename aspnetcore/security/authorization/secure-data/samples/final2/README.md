---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210105"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a>Wie Sie Build/Datenbeispiel sichere Benutzer ausführen.

* Legen Sie das Kennwort, mit dem Geheimnis-Manager-Tool:

  `dotnet user-secrets set SeedUserPW <pw>`

* Die Datenbank zu aktualisieren:

  `dotnet ef database update`

* Aktivieren von HTTPS im Projekt
