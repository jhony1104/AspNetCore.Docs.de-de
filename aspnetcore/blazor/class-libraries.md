---
title: ASP.net Core Razor Components-Klassenbibliotheken
author: guardrex
description: Erfahren Sie, wie Komponenten in blazor-Apps aus einer externen Komponentenbibliothek eingeschlossen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: 6e93d48bbc684845952c3db8935ccc8b190044b7
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030350"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="cf759-103">ASP.net Core Razor Components-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="cf759-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="cf759-104">Von [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="cf759-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="cf759-105">Komponenten können in einer Razor- [Klassenbibliothek (RCL)](xref:razor-pages/ui-class) Projekt übergreifend gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="cf759-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="cf759-106">Eine *Razor Components-Klassenbibliothek* kann in Folgendes eingeschlossen werden:</span><span class="sxs-lookup"><span data-stu-id="cf759-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="cf759-107">Ein anderes Projekt in der Projekt Mappe.</span><span class="sxs-lookup"><span data-stu-id="cf759-107">Another project in the solution.</span></span>
* <span data-ttu-id="cf759-108">Ein nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="cf759-108">A NuGet package.</span></span>
* <span data-ttu-id="cf759-109">Eine referenzierte .NET-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="cf759-109">A referenced .NET library.</span></span>

<span data-ttu-id="cf759-110">Ebenso wie Komponenten reguläre .NET-Typen sind, sind von einer RCL bereitgestellte Komponenten normale .NET-Assemblys.</span><span class="sxs-lookup"><span data-stu-id="cf759-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="cf759-111">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="cf759-111">Create an RCL</span></span>

<span data-ttu-id="cf759-112">Befolgen Sie die Anleitungen in <xref:blazor/get-started> diesem Artikel, um Ihre Umgebung für blazor zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="cf759-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cf759-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf759-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cf759-114">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="cf759-114">Create a new project.</span></span>
1. <span data-ttu-id="cf759-115">Wählen Sie **Razor-Klassenbibliothek**aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="cf759-116">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="cf759-116">Select **Next**.</span></span>
1. <span data-ttu-id="cf759-117">Wählen Sie im Dialogfeld **neue Razor-Klassenbibliothek erstellen** die Option **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="cf759-118">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="cf759-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cf759-119">In den Beispielen in diesem Thema wird der Projekt `MyComponentLib1`Name verwendet.</span><span class="sxs-lookup"><span data-stu-id="cf759-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="cf759-120">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-120">Select **Create**.</span></span>
1. <span data-ttu-id="cf759-121">Fügen Sie die RCL zu einer Projekt Mappe hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf759-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="cf759-122">Klicken Sie mit der rechten Maustaste auf die Lösung.</span><span class="sxs-lookup"><span data-stu-id="cf759-122">Right-click the solution.</span></span> <span data-ttu-id="cf759-123">Wählen Sie**vorhandenes Projekt** **Hinzufügen** > .</span><span class="sxs-lookup"><span data-stu-id="cf759-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="cf759-124">Navigieren Sie zur Projektdatei der RCL.</span><span class="sxs-lookup"><span data-stu-id="cf759-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="cf759-125">Wählen Sie die Projektdatei ( *. csproj*) der RCL aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="cf759-126">Fügen Sie einen Verweis auf die RCL aus der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf759-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="cf759-127">Klicken Sie mit der rechten Maustaste auf das App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="cf759-127">Right-click the app project.</span></span> <span data-ttu-id="cf759-128">Wählen Sie**Verweis** **Hinzufügen** > aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="cf759-129">Wählen Sie das RCL-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="cf759-129">Select the RCL project.</span></span> <span data-ttu-id="cf759-130">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cf759-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cf759-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cf759-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="cf759-132">Verwenden Sie die Vorlage für die Razor`razorclasslib`- **Klassenbibliothek** () mit dem Befehl [dotnet New](/dotnet/core/tools/dotnet-new) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="cf759-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="cf759-133">Im folgenden Beispiel wird eine RCL mit dem Namen `MyComponentLib1`erstellt.</span><span class="sxs-lookup"><span data-stu-id="cf759-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="cf759-134">Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cf759-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="cf759-135">Um die Bibliothek einem vorhandenen Projekt hinzuzufügen, verwenden Sie den Befehl [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="cf759-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="cf759-136">Im folgenden Beispiel wird die RCL der app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cf759-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="cf759-137">Führen Sie den folgenden Befehl aus dem Projektordner der APP mit dem Pfad zur Bibliothek aus:</span><span class="sxs-lookup"><span data-stu-id="cf759-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="cf759-138">Rcls werden für Client seitige apps nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cf759-138">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="cf759-139">In der aktuellen ASP.net Core 3,0 Preview sind Razor-Klassenbibliotheken nicht mit Client seitigen blazor-Apps kompatibel.</span><span class="sxs-lookup"><span data-stu-id="cf759-139">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="cf759-140">Verwenden Sie für Client seitige blazor-apps eine blazor-Komponentenbibliothek, die `blazorlib` von der Vorlage in einer Befehlsshell erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="cf759-140">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="cf759-141">Komponenten Bibliotheken, die `blazorlib` die Vorlage verwenden, können statische Dateien enthalten, wie z. b. Bilder, JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="cf759-141">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="cf759-142">Zum Zeitpunkt der Erstellung werden statische Dateien in die integrierte Assemblydatei ( *. dll*) eingebettet, wodurch die Nutzung der Komponenten ermöglicht wird, ohne sich Gedanken über das einschließen ihrer Ressourcen machen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="cf759-142">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="cf759-143">Alle Dateien, die `content` im Verzeichnis enthalten sind, werden als eingebettete Ressource gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="cf759-143">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="cf759-144">Verwenden einer Bibliotheks Komponente</span><span class="sxs-lookup"><span data-stu-id="cf759-144">Consume a library component</span></span>

<span data-ttu-id="cf759-145">Verwenden Sie einen der folgenden Ansätze, um Komponenten zu verwenden, die in einer Bibliothek in einem anderen Projekt definiert sind:</span><span class="sxs-lookup"><span data-stu-id="cf759-145">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="cf759-146">Verwenden Sie den vollständigen Typnamen mit dem-Namespace.</span><span class="sxs-lookup"><span data-stu-id="cf759-146">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="cf759-147">Verwenden Sie die [ \@using](xref:mvc/views/razor#using) -Direktive von Razor.</span><span class="sxs-lookup"><span data-stu-id="cf759-147">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="cf759-148">Einzelne Komponenten können anhand des Namens hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="cf759-148">Individual components can be added by name.</span></span>

<span data-ttu-id="cf759-149">In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport` -Komponente enthält.</span><span class="sxs-lookup"><span data-stu-id="cf759-149">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="cf759-150">Auf `SalesReport` die Komponente kann mit dem vollständigen Typnamen mit Namespace verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="cf759-150">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="cf759-151">Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using` -Direktive in den Gültigkeitsbereich eingefügt wird:</span><span class="sxs-lookup"><span data-stu-id="cf759-151">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="cf759-152">Fügen Sie `@using MyComponentLib1` die-Direktive in die *_Import. Razor* -Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein gesamtes Projekt verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="cf759-152">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="cf759-153">Fügen Sie die-Direktive zu einer *_Import. Razor* -Datei auf jeder Ebene hinzu, um den Namespace auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="cf759-153">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="cf759-154">Erstellen, Verpacken und versenden an nuget</span><span class="sxs-lookup"><span data-stu-id="cf759-154">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="cf759-155">Da es sich bei Komponenten Bibliotheken um .net-Standardbibliotheken handelt, unterscheidet sich das Verpacken und Versenden von Dateien an nuget nicht von der Verpackung und dem Versand von Bibliotheken an nuget.</span><span class="sxs-lookup"><span data-stu-id="cf759-155">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="cf759-156">Die Paket Erstellung erfolgt mithilfe des Befehls " [dotnet Pack](/dotnet/core/tools/dotnet-pack) " in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="cf759-156">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="cf759-157">Laden Sie das Paket mit dem Befehl " [dotnet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) " in eine Befehlsshell in NuGet hoch:</span><span class="sxs-lookup"><span data-stu-id="cf759-157">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="cf759-158">Wenn Sie die `blazorlib` Vorlage verwenden, werden statische Ressourcen in das nuget-Paket eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="cf759-158">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="cf759-159">Bibliotheks Consumer erhalten automatisch Skripts und Stylesheets, sodass Consumer die Ressourcen nicht manuell installieren müssen.</span><span class="sxs-lookup"><span data-stu-id="cf759-159">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="cf759-160">Erstellen einer Razor Components-Klassenbibliothek mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="cf759-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="cf759-161">Eine RCL kann statische Assets enthalten.</span><span class="sxs-lookup"><span data-stu-id="cf759-161">An RCL can include static assets.</span></span> <span data-ttu-id="cf759-162">Die statischen Ressourcen sind für jede app verfügbar, die die Bibliothek nutzt.</span><span class="sxs-lookup"><span data-stu-id="cf759-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="cf759-163">Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="cf759-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf759-164">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cf759-164">Additional resources</span></span>

* <xref:razor-pages/ui-class>
