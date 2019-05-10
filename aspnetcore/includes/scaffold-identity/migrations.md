<span data-ttu-id="41538-101">Der generierte Code für die Identität Datenbank erfordert [Entity Framework Core-Migrationen](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="41538-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="41538-102">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="41538-102">Create a migration and update the database.</span></span> <span data-ttu-id="41538-103">Führen Sie z. B. die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="41538-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="41538-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41538-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41538-105">In der Visual Studio **-Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="41538-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="41538-106">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="41538-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="41538-107">Der "CreateIdentitySchema"-Name-Parameter für die `Add-Migration` Befehl ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="41538-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="41538-108">`"CreateIdentitySchema"` Beschreibt die Migration.</span><span class="sxs-lookup"><span data-stu-id="41538-108">`"CreateIdentitySchema"` describes the migration.</span></span>
