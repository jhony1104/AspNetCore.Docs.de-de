<!-- THIS INCLUDE USED BY MVC AND RP -->
Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`:  Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (Date) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.
