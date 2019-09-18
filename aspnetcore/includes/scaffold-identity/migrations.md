Der generierte Identitätsdaten Bank Code erfordert [Entity Framework Core Migrationen](/ef/core/managing-schemas/migrations/). Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank. Führen Sie beispielsweise die folgenden Befehle aus:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio **-Paket-Manager-Konsole**:

```powershell
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Der Name-Parameter "Name" von "Name" für den `Add-Migration` Befehl ist willkürlich. `"CreateIdentitySchema"`Beschreibt die Migration.
