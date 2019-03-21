---
ms.openlocfilehash: 698a127120f7f52672ceb927ff24eb1b02f91521
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320279"
---
<span data-ttu-id="37886-101">Der generierte Code für die Identität Datenbank erfordert [Entity Framework Core-Migrationen](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="37886-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="37886-102">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="37886-102">Create a migration and update the database.</span></span> <span data-ttu-id="37886-103">Führen Sie z. B. die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="37886-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="37886-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37886-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37886-105">In der Visual Studio **-Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="37886-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="37886-106">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="37886-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="37886-107">Der "CreateIdentitySchema"-Name-Parameter für die `Add-Migration` Befehl ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="37886-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="37886-108">`"CreateIdentitySchema"` Beschreibt die Migration.</span><span class="sxs-lookup"><span data-stu-id="37886-108">`"CreateIdentitySchema"` describes the migration.</span></span>
