---
<span data-ttu-id="9236d-101">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-103">'Blazor'</span></span>
- <span data-ttu-id="9236d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-104">'Identity'</span></span>
- <span data-ttu-id="9236d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-106">'Razor'</span></span>
- <span data-ttu-id="9236d-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="9236d-108">Blazor-Formulare und -Validierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9236d-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="9236d-109">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9236d-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9236d-110">Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9236d-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="9236d-111">Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:</span><span class="sxs-lookup"><span data-stu-id="9236d-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="9236d-112">Ein Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="9236d-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="9236d-113">Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:</span><span class="sxs-lookup"><span data-stu-id="9236d-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="9236d-114">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9236d-114">In the preceding example:</span></span>

* <span data-ttu-id="9236d-115">Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="9236d-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="9236d-116">Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9236d-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="9236d-117">Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="9236d-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="9236d-118">Der `@bind-Value` der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente bindet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9236d-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="9236d-119">Die Modelleigenschaft (`exampleModel.Name`) an die `Value`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="9236d-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="9236d-120">Weitere Informationen zur Eigenschaftenbindung finden Sie unter <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="9236d-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="9236d-121">Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="9236d-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="9236d-122">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="9236d-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="9236d-123">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst Validierungsnachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="9236d-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="9236d-124">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).</span><span class="sxs-lookup"><span data-stu-id="9236d-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="9236d-125">Einige integrierte Eingabekomponenten sind verfügbar, um Benutzereingaben zu empfangen und zu validieren.</span><span class="sxs-lookup"><span data-stu-id="9236d-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="9236d-126">Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="9236d-127">In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.</span><span class="sxs-lookup"><span data-stu-id="9236d-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="9236d-128">Eingabekomponenten</span><span class="sxs-lookup"><span data-stu-id="9236d-128">Input component</span></span> | <span data-ttu-id="9236d-129">Wird gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="9236d-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="9236d-130">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-132">'Blazor'</span></span>
- <span data-ttu-id="9236d-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-133">'Identity'</span></span>
- <span data-ttu-id="9236d-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-135">'Razor'</span></span>
- <span data-ttu-id="9236d-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-137">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-139">'Blazor'</span></span>
- <span data-ttu-id="9236d-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-140">'Identity'</span></span>
- <span data-ttu-id="9236d-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-142">'Razor'</span></span>
- <span data-ttu-id="9236d-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-144">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-146">'Blazor'</span></span>
- <span data-ttu-id="9236d-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-147">'Identity'</span></span>
- <span data-ttu-id="9236d-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-149">'Razor'</span></span>
- <span data-ttu-id="9236d-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-151">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-153">'Blazor'</span></span>
- <span data-ttu-id="9236d-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-154">'Identity'</span></span>
- <span data-ttu-id="9236d-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-156">'Razor'</span></span>
- <span data-ttu-id="9236d-157">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-158">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-160">'Blazor'</span></span>
- <span data-ttu-id="9236d-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-161">'Identity'</span></span>
- <span data-ttu-id="9236d-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-163">'Razor'</span></span>
- <span data-ttu-id="9236d-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-164">'SignalR' uid:</span></span> 

<span data-ttu-id="9236d-165">-------- | --- title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-167">'Blazor'</span></span>
- <span data-ttu-id="9236d-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-168">'Identity'</span></span>
- <span data-ttu-id="9236d-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-170">'Razor'</span></span>
- <span data-ttu-id="9236d-171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-172">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-174">'Blazor'</span></span>
- <span data-ttu-id="9236d-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-175">'Identity'</span></span>
- <span data-ttu-id="9236d-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-177">'Razor'</span></span>
- <span data-ttu-id="9236d-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-179">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-181">'Blazor'</span></span>
- <span data-ttu-id="9236d-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-182">'Identity'</span></span>
- <span data-ttu-id="9236d-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-184">'Razor'</span></span>
- <span data-ttu-id="9236d-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-186">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-188">'Blazor'</span></span>
- <span data-ttu-id="9236d-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-189">'Identity'</span></span>
- <span data-ttu-id="9236d-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-191">'Razor'</span></span>
- <span data-ttu-id="9236d-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-193">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-195">'Blazor'</span></span>
- <span data-ttu-id="9236d-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-196">'Identity'</span></span>
- <span data-ttu-id="9236d-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-198">'Razor'</span></span>
- <span data-ttu-id="9236d-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-200">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-202">'Blazor'</span></span>
- <span data-ttu-id="9236d-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-203">'Identity'</span></span>
- <span data-ttu-id="9236d-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-205">'Razor'</span></span>
- <span data-ttu-id="9236d-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9236d-207">title: „Blazor-Formulare und -Validierung in ASP.NET Core“ Autor: Beschreibung: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarien in Blazor verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="9236d-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9236d-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9236d-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9236d-209">'Blazor'</span></span>
- <span data-ttu-id="9236d-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9236d-210">'Identity'</span></span>
- <span data-ttu-id="9236d-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9236d-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="9236d-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9236d-212">'Razor'</span></span>
- <span data-ttu-id="9236d-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="9236d-213">'SignalR' uid:</span></span> 

<span data-ttu-id="9236d-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="9236d-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="9236d-215">Alle Eingabekomponenten einschließlich <xref:Microsoft.AspNetCore.Components.Forms.EditForm> unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="9236d-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="9236d-216">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9236d-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="9236d-217">Eingabekomponenten stellen Standardverhalten für das Validieren bei Bearbeitung zur Verfügung und ändern ihre CSS-Klasse, um den Feldzustand darzustellen.</span><span class="sxs-lookup"><span data-stu-id="9236d-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="9236d-218">Einige Komponenten beinhalten hilfreiche Parsinglogik.</span><span class="sxs-lookup"><span data-stu-id="9236d-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="9236d-219"><xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> und <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="9236d-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="9236d-220">Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).</span><span class="sxs-lookup"><span data-stu-id="9236d-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="9236d-221">Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="9236d-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="9236d-222">Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="9236d-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="9236d-223">Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:</span><span class="sxs-lookup"><span data-stu-id="9236d-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="9236d-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> erstellt <xref:Microsoft.AspNetCore.Components.Forms.EditContext> als [kaskadierenden Wert](xref:blazor/components#cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten.</span><span class="sxs-lookup"><span data-stu-id="9236d-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="9236d-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> stellt auch für gültige und ungültige Sendevorgänge (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>) geeignete Ereignisse zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9236d-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="9236d-226">Alternativ können Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> verwenden, um die Validierung auszulösen und Feldwerte mit benutzerdefiniertem Validierungscode zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9236d-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="9236d-227">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9236d-227">In the following example:</span></span>

* <span data-ttu-id="9236d-228">Die `HandleSubmit`-Methode wird ausgeführt, wenn auf die Schaltfläche **Senden** geklickt wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="9236d-229">Das Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars validiert.</span><span class="sxs-lookup"><span data-stu-id="9236d-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="9236d-230">Das Formular wird dann weiter validiert, indem <xref:Microsoft.AspNetCore.Components.Forms.EditContext> an die `ServerValidate`-Methode übergeben wird, die einen Web-API-Endpunkt auf dem Server aufruft (*wird hier nicht gezeigt*).</span><span class="sxs-lookup"><span data-stu-id="9236d-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="9236d-231">Je nach Ergebnis der client- und serverseitigen Validierung, indem `isValid` überprüft wird, wird weiterer Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9236d-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="9236d-232">InputText basierend auf dem Eingabeereignis</span><span class="sxs-lookup"><span data-stu-id="9236d-232">InputText based on the input event</span></span>

<span data-ttu-id="9236d-233">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="9236d-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="9236d-234">Erstellen Sie eine Komponente mit dem folgenden Markup, und verwenden Sie die Komponente genauso, wie <xref:Microsoft.AspNetCore.Components.Forms.InputText> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="9236d-235">Verwenden von Optionsfeldern</span><span class="sxs-lookup"><span data-stu-id="9236d-235">Work with radio buttons</span></span>

<span data-ttu-id="9236d-236">Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="9236d-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="9236d-237">Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds.</span><span class="sxs-lookup"><span data-stu-id="9236d-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="9236d-238">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="9236d-238">The following example shows how to:</span></span>

* <span data-ttu-id="9236d-239">Verarbeiten von Datenbindungen für eine Optionsfeldgruppe</span><span class="sxs-lookup"><span data-stu-id="9236d-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="9236d-240">Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente</span><span class="sxs-lookup"><span data-stu-id="9236d-240">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="9236d-241">Die folgende <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:</span><span class="sxs-lookup"><span data-stu-id="9236d-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="9236d-242">Validierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="9236d-242">Validation support</span></span>

<span data-ttu-id="9236d-243">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse an.</span><span class="sxs-lookup"><span data-stu-id="9236d-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="9236d-244">Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9236d-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="9236d-245">Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="9236d-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="9236d-246">Die ASP.NET Core-Implementierung können Sie sich in der Referenzquelle ansehen: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9236d-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="9236d-247"> führt zwei Validierungstypen aus:</span><span class="sxs-lookup"><span data-stu-id="9236d-247"> performs two types of validation:</span></span>

* <span data-ttu-id="9236d-248">Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt.</span><span class="sxs-lookup"><span data-stu-id="9236d-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="9236d-249">Während der Feldvalidierung ordnet die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="9236d-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="9236d-250">*Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="9236d-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="9236d-251">Während der Modellvalidierung versucht die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="9236d-252">Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9236d-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="9236d-253">Komponenten der Validierungszusammenfassung und der Validierungsnachricht</span><span class="sxs-lookup"><span data-stu-id="9236d-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="9236d-254">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="9236d-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="9236d-255">Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:</span><span class="sxs-lookup"><span data-stu-id="9236d-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="9236d-256">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9236d-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="9236d-257">Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="9236d-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="9236d-258">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>- und <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponenten unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="9236d-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="9236d-259">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9236d-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="9236d-260">Benutzerdefinierte Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="9236d-260">Custom validation attributes</span></span>

<span data-ttu-id="9236d-261">Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9236d-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="9236d-262">Validierungspakete für Datenanmerkungen in Blazor</span><span class="sxs-lookup"><span data-stu-id="9236d-262">Blazor data annotations validation package</span></span>

<span data-ttu-id="9236d-263">Bei [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) handelt es sich um ein Paket, das Lücken bei der Validierung mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente beheben kann.</span><span class="sxs-lookup"><span data-stu-id="9236d-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="9236d-264">Das Paket ist aktuell *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="9236d-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="9236d-265">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="9236d-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="9236d-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="9236d-267">Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen.</span><span class="sxs-lookup"><span data-stu-id="9236d-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="9236d-268">Das *experimentelle* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)-Paket führt ein zusätzliches Validierungsattribut ein (`ComparePropertyAttribute`), das diese Begrenzungen umgehen kann.</span><span class="sxs-lookup"><span data-stu-id="9236d-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="9236d-269">In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attributs.</span><span class="sxs-lookup"><span data-stu-id="9236d-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="9236d-270">Verschachtelte Modelle, Sammlungstypen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="9236d-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="9236d-271"> bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="9236d-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="9236d-272"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.</span><span class="sxs-lookup"><span data-stu-id="9236d-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="9236d-273">Verwenden Sie den vom *experimentellen* Paket [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellten `ObjectGraphDataAnnotationsValidator`, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexen Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="9236d-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="9236d-274">Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9236d-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="9236d-275">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="9236d-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="9236d-276">*Starship.cs:*</span><span class="sxs-lookup"><span data-stu-id="9236d-276">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="9236d-277">*ShipDescription.cs:*</span><span class="sxs-lookup"><span data-stu-id="9236d-277">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="9236d-278">Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung</span><span class="sxs-lookup"><span data-stu-id="9236d-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="9236d-279">So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:</span><span class="sxs-lookup"><span data-stu-id="9236d-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="9236d-280">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="9236d-281">Validieren Sie das Formular im <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="9236d-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="9236d-282">Heben Sie die Einbindung des Ereignishandlers in der `Dispose`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="9236d-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="9236d-283">Weitere Informationen finden Sie unter <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="9236d-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="9236d-284">Im vorherigen Beispiel ist `formInvalid` in folgenden Fällen auf `false` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="9236d-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="9236d-285">Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="9236d-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="9236d-286">Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.</span><span class="sxs-lookup"><span data-stu-id="9236d-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="9236d-287">Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat.</span><span class="sxs-lookup"><span data-stu-id="9236d-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="9236d-288">Dieses Szenario lässt sich folgendermaßen auflösen:</span><span class="sxs-lookup"><span data-stu-id="9236d-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="9236d-289">Verwenden Sie keine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente des Formulars.</span><span class="sxs-lookup"><span data-stu-id="9236d-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="9236d-290">Lassen Sie die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.</span><span class="sxs-lookup"><span data-stu-id="9236d-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="9236d-291">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="9236d-291">Troubleshoot</span></span>

> <span data-ttu-id="9236d-292">InvalidOperationException: EditForm erfordert einen Model-Parameter oder einen EditContext-Parameter, aber nicht beides.</span><span class="sxs-lookup"><span data-stu-id="9236d-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="9236d-293">Vergewissern Sie sich, dass <xref:Microsoft.AspNetCore.Components.Forms.EditForm> über ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>- oder <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Element verfügt.</span><span class="sxs-lookup"><span data-stu-id="9236d-293">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="9236d-294">Wenn Sie dem Formular ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>-Element zuweisen, vergewissern Sie sich, dass der Modelltyp instanziiert wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="9236d-294">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
