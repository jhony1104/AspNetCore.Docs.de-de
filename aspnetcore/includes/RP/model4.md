<a name="codegenerator"></a> In der folgenden Tabelle sind die Parameter des ASP.NET Core-Codegenerators detailliert aufgeführt:

| Parameter               | Beschreibung|
| ----------------- | ------------ |
| -m  | Der Name des Modells. |
| -dc  | Die `DbContext`-Klasse, die verwendet werden soll. |
| -udl | Verwendung des Standardlayouts. |
| -outDir | Der relative Ausgabeordnerpfad zur Erstellung der Ansichten. |
| --referenceScriptLibraries | Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu. |

Verwenden Sie den `h`-Switch um Hilfe für den `aspnet-codegenerator razorpage`-Befehl zu erhalten:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Weitere Informationen finden Sie unter [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
