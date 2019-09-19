<a name="codegenerator"></a> <span data-ttu-id="79381-101">In der folgenden Tabelle sind die Parameter des ASP.NET Core-Codegenerators detailliert aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="79381-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="79381-102">Parameter</span><span class="sxs-lookup"><span data-stu-id="79381-102">Parameter</span></span>               | <span data-ttu-id="79381-103">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="79381-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="79381-104">-m</span><span class="sxs-lookup"><span data-stu-id="79381-104">-m</span></span>  | <span data-ttu-id="79381-105">Der Name des Modells.</span><span class="sxs-lookup"><span data-stu-id="79381-105">The name of the model.</span></span> |
| <span data-ttu-id="79381-106">-dc</span><span class="sxs-lookup"><span data-stu-id="79381-106">-dc</span></span>  | <span data-ttu-id="79381-107">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="79381-107">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="79381-108">-udl</span><span class="sxs-lookup"><span data-stu-id="79381-108">-udl</span></span> | <span data-ttu-id="79381-109">Verwendung des Standardlayouts.</span><span class="sxs-lookup"><span data-stu-id="79381-109">Use the default layout.</span></span> |
| <span data-ttu-id="79381-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="79381-110">-outDir</span></span> | <span data-ttu-id="79381-111">Der relative Ausgabeordnerpfad zur Erstellung der Ansichten.</span><span class="sxs-lookup"><span data-stu-id="79381-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="79381-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="79381-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="79381-113">Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="79381-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="79381-114">Verwenden Sie den `h`-Switch um Hilfe für den `aspnet-codegenerator razorpage`-Befehl zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="79381-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="79381-115">Weitere Informationen finden Sie unter [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="79381-115">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span></span> 