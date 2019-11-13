---
title: ASP.net Core Razor Components-Klassenbibliotheken
author: guardrex
description: Erfahren Sie, wie Komponenten in Blazor-Apps aus einer externen Komponentenbibliothek eingeschlossen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
no-loc:
- Blazor
uid: blazor/class-libraries
ms.openlocfilehash: d4cc4124c9dc28ed6da0923b919919df4965f89f
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962713"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="65a9d-103">ASP.net Core Razor Components-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="65a9d-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="65a9d-104">Von [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="65a9d-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="65a9d-105">Komponenten können in einer Razor- [Klassenbibliothek (RCL)](xref:razor-pages/ui-class) Projekt übergreifend gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="65a9d-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="65a9d-106">Eine *Razor Components-Klassenbibliothek* kann in Folgendes eingeschlossen werden:</span><span class="sxs-lookup"><span data-stu-id="65a9d-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="65a9d-107">Ein anderes Projekt in der Projekt Mappe.</span><span class="sxs-lookup"><span data-stu-id="65a9d-107">Another project in the solution.</span></span>
* <span data-ttu-id="65a9d-108">Ein nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="65a9d-108">A NuGet package.</span></span>
* <span data-ttu-id="65a9d-109">Eine referenzierte .NET-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="65a9d-109">A referenced .NET library.</span></span>

<span data-ttu-id="65a9d-110">Ebenso wie Komponenten reguläre .NET-Typen sind, sind von einer RCL bereitgestellte Komponenten normale .NET-Assemblys.</span><span class="sxs-lookup"><span data-stu-id="65a9d-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="65a9d-111">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="65a9d-111">Create an RCL</span></span>

<span data-ttu-id="65a9d-112">Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um Ihre Umgebung für Blazorzu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="65a9d-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65a9d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65a9d-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="65a9d-114">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="65a9d-114">Create a new project.</span></span>
1. <span data-ttu-id="65a9d-115">Wählen Sie **Razor-Klassenbibliothek**aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="65a9d-116">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="65a9d-116">Select **Next**.</span></span>
1. <span data-ttu-id="65a9d-117">Wählen Sie im Dialogfeld **neue Razor-Klassenbibliothek erstellen** die Option **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="65a9d-118">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="65a9d-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="65a9d-119">In den Beispielen in diesem Thema wird der Projektname `MyComponentLib1`verwendet.</span><span class="sxs-lookup"><span data-stu-id="65a9d-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="65a9d-120">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-120">Select **Create**.</span></span>
1. <span data-ttu-id="65a9d-121">Fügen Sie die RCL zu einer Projekt Mappe hinzu:</span><span class="sxs-lookup"><span data-stu-id="65a9d-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="65a9d-122">Klicken Sie mit der rechten Maustaste auf die Lösung.</span><span class="sxs-lookup"><span data-stu-id="65a9d-122">Right-click the solution.</span></span> <span data-ttu-id="65a9d-123">Wählen Sie > **vorhandenes Projekt** **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="65a9d-124">Navigieren Sie zur Projektdatei der RCL.</span><span class="sxs-lookup"><span data-stu-id="65a9d-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="65a9d-125">Wählen Sie die Projektdatei ( *. csproj*) der RCL aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="65a9d-126">Fügen Sie einen Verweis auf die RCL aus der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="65a9d-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="65a9d-127">Klicken Sie mit der rechten Maustaste auf das App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="65a9d-127">Right-click the app project.</span></span> <span data-ttu-id="65a9d-128">Wählen Sie > **Verweis** **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="65a9d-129">Wählen Sie das RCL-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="65a9d-129">Select the RCL project.</span></span> <span data-ttu-id="65a9d-130">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="65a9d-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="65a9d-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="65a9d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="65a9d-132">Verwenden Sie die Vorlage für die **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [dotnet New](/dotnet/core/tools/dotnet-new) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="65a9d-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="65a9d-133">Im folgenden Beispiel wird eine RCL namens `MyComponentLib1`erstellt.</span><span class="sxs-lookup"><span data-stu-id="65a9d-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="65a9d-134">Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="65a9d-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="65a9d-135">Um die Bibliothek einem vorhandenen Projekt hinzuzufügen, verwenden Sie den Befehl [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="65a9d-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="65a9d-136">Im folgenden Beispiel wird die RCL der app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="65a9d-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="65a9d-137">Führen Sie den folgenden Befehl aus dem Projektordner der APP mit dem Pfad zur Bibliothek aus:</span><span class="sxs-lookup"><span data-stu-id="65a9d-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="65a9d-138">Verwenden einer Bibliotheks Komponente</span><span class="sxs-lookup"><span data-stu-id="65a9d-138">Consume a library component</span></span>

<span data-ttu-id="65a9d-139">Verwenden Sie einen der folgenden Ansätze, um Komponenten zu verwenden, die in einer Bibliothek in einem anderen Projekt definiert sind:</span><span class="sxs-lookup"><span data-stu-id="65a9d-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="65a9d-140">Verwenden Sie den vollständigen Typnamen mit dem-Namespace.</span><span class="sxs-lookup"><span data-stu-id="65a9d-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="65a9d-141">Verwenden Sie die [\@using](xref:mvc/views/razor#using) -Direktive von Razor.</span><span class="sxs-lookup"><span data-stu-id="65a9d-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="65a9d-142">Einzelne Komponenten können anhand des Namens hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="65a9d-142">Individual components can be added by name.</span></span>

<span data-ttu-id="65a9d-143">In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport` Komponente enthält.</span><span class="sxs-lookup"><span data-stu-id="65a9d-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="65a9d-144">Auf die `SalesReport` Komponente kann mit dem vollständigen Typnamen mit Namespace verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="65a9d-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="65a9d-145">Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using`-Direktive in den Gültigkeitsbereich eingebunden ist:</span><span class="sxs-lookup"><span data-stu-id="65a9d-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="65a9d-146">Fügen Sie die `@using MyComponentLib1`-Direktive in der *_Import. Razor* -Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein gesamtes Projekt verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="65a9d-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="65a9d-147">Fügen Sie die-Direktive einer *_Import Razor* -Datei auf jeder Ebene hinzu, um den Namespace auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="65a9d-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="65a9d-148">Erstellen, Verpacken und versenden an nuget</span><span class="sxs-lookup"><span data-stu-id="65a9d-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="65a9d-149">Da es sich bei Komponenten Bibliotheken um .net-Standardbibliotheken handelt, unterscheidet sich das Verpacken und Versenden von Dateien an nuget nicht von der Verpackung und dem Versand von Bibliotheken an nuget.</span><span class="sxs-lookup"><span data-stu-id="65a9d-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="65a9d-150">Die Paket Erstellung erfolgt mithilfe des Befehls " [dotnet Pack](/dotnet/core/tools/dotnet-pack) " in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="65a9d-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="65a9d-151">Laden Sie das Paket mit dem Befehl [dotnet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) in eine Befehlsshell in NuGet hoch.</span><span class="sxs-lookup"><span data-stu-id="65a9d-151">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="65a9d-152">Erstellen einer Razor Components-Klassenbibliothek mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="65a9d-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="65a9d-153">Eine RCL kann statische Assets enthalten.</span><span class="sxs-lookup"><span data-stu-id="65a9d-153">An RCL can include static assets.</span></span> <span data-ttu-id="65a9d-154">Die statischen Ressourcen sind für jede app verfügbar, die die Bibliothek nutzt.</span><span class="sxs-lookup"><span data-stu-id="65a9d-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="65a9d-155">Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="65a9d-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65a9d-156">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="65a9d-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
