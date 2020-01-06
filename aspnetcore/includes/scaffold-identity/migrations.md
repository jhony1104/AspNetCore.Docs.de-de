Der generierte Identitätsdaten Bank Code erfordert [Entity Framework Core Migrationen](/ef/core/managing-schemas/migrations/). Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank. Führen Sie beispielsweise die folgenden Befehle aus:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio **-Paket-Manager-Konsole**:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Der Name-Parameter "Name" für den Befehl "`Add-Migration`" ist willkürlich. in `"CreateIdentitySchema"` wird die Migration beschrieben.
