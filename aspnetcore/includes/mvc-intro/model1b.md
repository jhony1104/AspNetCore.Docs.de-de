Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Das `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.
* `[DataType(DataType.Date)]`:  Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.