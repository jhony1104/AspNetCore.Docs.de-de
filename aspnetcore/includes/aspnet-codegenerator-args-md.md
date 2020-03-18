<!-- Options common to Razor Pages and Controller -->
| Option               | Beschreibung|
| ----------------- | ------------ |
| --model oder -m  | Die Modellklasse, die verwendet werden soll. |
| --dataContext oder -dc  | Die `DbContext`-Klasse, die verwendet werden soll. |
| --bootstrapVersion oder -b  | Gibt die Bootstrap-Version an. Gültige Werte sind `3` und `4`. Der Standardwert ist `4`. Falls benötigt und noch nicht vorhanden, wird ein *wwwroot*-Verzeichnis erstellt, das die Bootstrap-Dateien der angegebenen Version enthält. |
| --referenceScriptLibraries oder -scripts |  Verweist auf Skriptbibliotheken in den erstellten Ansichten. Fügt `_ValidationScriptsPartial` zum Bearbeiten und Erstellen von Seiten hinzu. |
| --layout oder -l | Benutzerdefinierte Layoutseite, die verwendet werden soll. |
| --useDefaultLayout oder -udl | Verwendet das Standardlayout für die Ansichten. |
| --force oder -f | Überschreibt vorhandene Dateien. |
| --relativeFolderPath oder -outDir | Der relative Ausgabeordnerpfad des Projekts, in dem die Dateien erstellt werden. Wenn dieser nicht angegeben wird, werden die Dateien im Projektordner erstellt. |