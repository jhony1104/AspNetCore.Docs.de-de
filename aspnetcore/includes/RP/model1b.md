<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="d7e9c-101">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="d7e9c-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="d7e9c-102">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="d7e9c-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="d7e9c-103">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d7e9c-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="d7e9c-104">`[DataType(DataType.Date)]`:  Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Date) an.</span><span class="sxs-lookup"><span data-stu-id="d7e9c-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="d7e9c-105">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="d7e9c-105">With this attribute:</span></span>

  * <span data-ttu-id="d7e9c-106">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="d7e9c-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="d7e9c-107">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="d7e9c-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d7e9c-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="d7e9c-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>