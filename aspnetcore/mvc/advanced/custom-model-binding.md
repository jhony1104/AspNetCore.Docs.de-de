---
title: Anpassen von Modellbindungen in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr darüber, wie Controlleraktionen durch Modellbindungen direkt mit den Modelltypen in ASP.NET Core arbeiten.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 91f42393ffee3249f9167e10eaea7b279a7cb70b
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878415"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="7b7ce-103">Anpassen von Modellbindungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b7ce-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="7b7ce-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7b7ce-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7b7ce-105">Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="7b7ce-106">Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="7b7ce-107">Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).</span><span class="sxs-lookup"><span data-stu-id="7b7ce-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="7b7ce-108">[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/).</span><span class="sxs-lookup"><span data-stu-id="7b7ce-108">[View or download sample from GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="7b7ce-109">Standardmodellbindungseinschränkungen</span><span class="sxs-lookup"><span data-stu-id="7b7ce-109">Default model binder limitations</span></span>

<span data-ttu-id="7b7ce-110">Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="7b7ce-111">Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="7b7ce-112">Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="7b7ce-113">Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="7b7ce-114">Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="7b7ce-115">Übersicht: Modellbindung</span><span class="sxs-lookup"><span data-stu-id="7b7ce-115">Model binding review</span></span>

<span data-ttu-id="7b7ce-116">Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="7b7ce-117">Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="7b7ce-118">Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="7b7ce-119">Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="7b7ce-120">Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="7b7ce-121">Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="7b7ce-122">Betrachten Sie [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder), das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="7b7ce-123">Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="7b7ce-124">Arbeiten mit ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="7b7ce-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="7b7ce-125">Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="7b7ce-126">Das folgende Bild kann beispielsweise als Zeichenfolge codiert werden:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="7b7ce-127">![.NET-Bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="7b7ce-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="7b7ce-128">Ein kleiner Anteil der codierten Zeichenfolge wird in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="7b7ce-129">![.NET-Bot codiert](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span><span class="sxs-lookup"><span data-stu-id="7b7ce-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="7b7ce-130">Befolgen Sie die Anweisungen in der [README-Datei des Beispiels](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md), um die Base64-codierte Zeichenfolge in eine Datei zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-130">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="7b7ce-131">ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="7b7ce-132">[ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider), der [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) implementiert, ordnet `byte[]`-Argumente `ByteArrayModelBinder` zu:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="7b7ce-133">Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute) verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="7b7ce-134">In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="7b7ce-135">Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="7b7ce-136">![Postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="7b7ce-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="7b7ce-137">Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="7b7ce-138">Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="7b7ce-139">Beispiel für eine benutzerdefinierte Modellbindung</span><span class="sxs-lookup"><span data-stu-id="7b7ce-139">Custom model binder sample</span></span>

<span data-ttu-id="7b7ce-140">In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="7b7ce-141">Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente</span><span class="sxs-lookup"><span data-stu-id="7b7ce-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="7b7ce-142">Abrufen der verknüpften Entität mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7b7ce-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="7b7ce-143">Übergeben der verknüpften Entität als Argument an die Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="7b7ce-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="7b7ce-144">In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="7b7ce-145">Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="7b7ce-146">Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-146">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="7b7ce-147">Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-147">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="7b7ce-148">Die Klasse stellt keine bewährte Methode für ein Suchszenario dar.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-148">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="7b7ce-149">Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-149">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="7b7ce-150">Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-150">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="7b7ce-151">Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-151">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="7b7ce-152">Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-152">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="7b7ce-153">Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-153">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="7b7ce-154">Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-154">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="7b7ce-155">Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-155">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="7b7ce-156">Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-156">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="7b7ce-157">Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-157">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="7b7ce-158">Implementieren von ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="7b7ce-158">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="7b7ce-159">Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-159">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="7b7ce-160">So werden die integrierten Frameworkbindungen implementiert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-160">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="7b7ce-161">Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-161">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="7b7ce-162">Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-162">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="7b7ce-163">Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-163">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="7b7ce-164">Hinweis: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-164">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="7b7ce-165">`BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI).</span><span class="sxs-lookup"><span data-stu-id="7b7ce-165">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="7b7ce-166">`AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-166">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="7b7ce-167">Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-167">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="7b7ce-168">Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-168">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="7b7ce-169">Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-169">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="7b7ce-170">Der erste Anbieter, der eine Bindung zurückgibt, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-170">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="7b7ce-171">In der folgenden Abbildung werden die Standardmodellbindungen des Debuggers gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-171">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="7b7ce-172">![Standardmodellbindung](custom-model-binding/images/default-model-binders.png "default model binders")</span><span class="sxs-lookup"><span data-stu-id="7b7ce-172">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="7b7ce-173">Wenn Sie Ihren Anbieter am Ende der Auflistung hinzufügen, kann es passieren, dass ein integrierter Modellbindung aufgerufen wird, bevor Ihre benutzerdefinierte Bindung an die Reihe kommt.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-173">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="7b7ce-174">In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er auch tatsächlich für `Author`-Aktionsargumente verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-174">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a><span data-ttu-id="7b7ce-175">Polymorphe Modellbindung</span><span class="sxs-lookup"><span data-stu-id="7b7ce-175">Polymorphic model binding</span></span>

<span data-ttu-id="7b7ce-176">Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-176">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="7b7ce-177">Eine benutzerdefinierte Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-177">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="7b7ce-178">Wenn dieser Ansatz nicht erforderlich ist, empfiehlt es sich, die polymorphe Modellbindung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-178">Unless this approach is required, we recommend avoiding polymorphic model binding.</span></span> <span data-ttu-id="7b7ce-179">Die polymorphe Modellbindung erschwert es, Informationen über die gebundenen Modelle zu bekommen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-179">Polymorphic model binding makes it difficult to reason about the bound models.</span></span> <span data-ttu-id="7b7ce-180">Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-180">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

<span data-ttu-id="7b7ce-181">Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-181">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="7b7ce-182">Eine benutzerdefinierte Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-182">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="7b7ce-183">Polymorphe Modellbindung:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-183">Polymorphic model binding:</span></span>

* <span data-ttu-id="7b7ce-184">Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-184">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="7b7ce-185">Erschwert es, Informationen über die gebundenen Modelle zu bekommen.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-185">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="7b7ce-186">Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-186">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="7b7ce-187">Empfehlungen und bewährte Methoden</span><span class="sxs-lookup"><span data-stu-id="7b7ce-187">Recommendations and best practices</span></span>

<span data-ttu-id="7b7ce-188">Benutzerdefinierte Modellbindungen:</span><span class="sxs-lookup"><span data-stu-id="7b7ce-188">Custom model binders:</span></span>

- <span data-ttu-id="7b7ce-189">Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="7b7ce-189">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="7b7ce-190">Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-190">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="7b7ce-191">Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-191">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="7b7ce-192">Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) ist oft eine sinnvollere Option.</span><span class="sxs-lookup"><span data-stu-id="7b7ce-192">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
