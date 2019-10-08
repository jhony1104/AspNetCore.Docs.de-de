# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **Workerdienst** aus. Klicken Sie auf **Weiter**.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Wählen Sie **Erstellen** aus.
1. Wählen Sie im Dialogfeld **Neuen Workerdienst erstellen** **Erstellen** aus.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie in der Randleiste unter **.NET Core** den Eintrag **App** aus.
1. Wählen Sie unter **ASP.NET Core** den Eintrag **Worker** aus. Klicken Sie auf **Weiter**.
1. Wählen Sie **.NET Core 3.0** als **Zielframework** aus. Klicken Sie auf **Weiter**.
1. Geben Sie im Feld **Projektname** einen Namen an. Wählen Sie **Erstellen** aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf. Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorker` erstellt. Der Ordner für die App `ContosoWorker` wird automatisch erstellt, wenn der Befehl ausgeführt wird.

```dotnetcli
dotnet new worker -o ContosoWorker
```
