<span data-ttu-id="591bf-101">Der generierte Identitätsdaten Bank Code erfordert [Entity Framework Core Migrationen](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="591bf-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="591bf-102">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="591bf-102">Create a migration and update the database.</span></span> <span data-ttu-id="591bf-103">Führen Sie beispielsweise die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="591bf-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="591bf-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591bf-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="591bf-105">In der Visual Studio- **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="591bf-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="591bf-106">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="591bf-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="591bf-107">Der Name-Parameter "Name" für den Befehl "`Add-Migration`" ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="591bf-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="591bf-108">in `"CreateIdentitySchema"` wird die Migration beschrieben.</span><span class="sxs-lookup"><span data-stu-id="591bf-108">`"CreateIdentitySchema"` describes the migration.</span></span>
