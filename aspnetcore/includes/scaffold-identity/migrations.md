---
ms.openlocfilehash: 698a127120f7f52672ceb927ff24eb1b02f91521
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320279"
---
Der generierte Code für die Identität Datenbank erfordert [Entity Framework Core-Migrationen](/ef/core/managing-schemas/migrations/). Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank. Führen Sie z. B. die folgenden Befehle ein:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio **-Paket-Manager-Konsole**:

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Der "CreateIdentitySchema"-Name-Parameter für die `Add-Migration` Befehl ist willkürlich. `"CreateIdentitySchema"` Beschreibt die Migration.
