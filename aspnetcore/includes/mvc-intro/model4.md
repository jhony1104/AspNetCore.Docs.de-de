Die folgende Tabelle zeigt die Details der Parameter des Codegenerators von ASP.NET:

| Parameter               | BESCHREIBUNG|
| ----------------- | ------------ |
| -m  | Der Name des Modells. |
| -dc  | Der Datenkontext. |
| -udl | Verwendung des Standardlayouts. |
| --relativeFolderPath | Der relative Ausgabeordnerpfad zur Erstellung der Ansichten. |
| --useDefaultLayout | Das Standardlayout sollte für die Ansichten verwendet werden. |
| --referenceScriptLibraries | Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu. |

Verwenden Sie den `h`-Switch um Hilfe für den `aspnet-codegenerator controller`-Befehl zu erhalten:

```console
dotnet aspnet-codegenerator controller -h
```