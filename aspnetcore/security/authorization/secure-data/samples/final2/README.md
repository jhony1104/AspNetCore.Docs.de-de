---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210105"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="56bdd-101">Wie Sie Build/Datenbeispiel sichere Benutzer ausführen.</span><span class="sxs-lookup"><span data-stu-id="56bdd-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="56bdd-102">Legen Sie das Kennwort, mit dem Geheimnis-Manager-Tool:</span><span class="sxs-lookup"><span data-stu-id="56bdd-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="56bdd-103">Die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="56bdd-103">Update the database:</span></span>

  `dotnet ef database update`

* <span data-ttu-id="56bdd-104">Aktivieren von HTTPS im Projekt</span><span class="sxs-lookup"><span data-stu-id="56bdd-104">Enable HTTPS in the project</span></span>
