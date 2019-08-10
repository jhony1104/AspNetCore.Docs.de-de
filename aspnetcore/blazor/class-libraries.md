---
title: ASP.net Core Razor Components-Klassenbibliotheken
author: guardrex
description: Erfahren Sie, wie Komponenten in blazor-Apps aus einer externen Komponentenbibliothek eingeschlossen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948440"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="ad065-103">ASP.net Core Razor Components-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="ad065-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="ad065-104">Von [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="ad065-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="ad065-105">Komponenten können in einer Razor- [Klassenbibliothek (RCL)](xref:razor-pages/ui-class) Projekt übergreifend gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="ad065-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="ad065-106">Eine *Razor Components-Klassenbibliothek* kann in Folgendes eingeschlossen werden:</span><span class="sxs-lookup"><span data-stu-id="ad065-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="ad065-107">Ein anderes Projekt in der Projekt Mappe.</span><span class="sxs-lookup"><span data-stu-id="ad065-107">Another project in the solution.</span></span>
* <span data-ttu-id="ad065-108">Ein nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="ad065-108">A NuGet package.</span></span>
* <span data-ttu-id="ad065-109">Eine referenzierte .NET-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="ad065-109">A referenced .NET library.</span></span>

<span data-ttu-id="ad065-110">Ebenso wie Komponenten reguläre .NET-Typen sind, sind von einer RCL bereitgestellte Komponenten normale .NET-Assemblys.</span><span class="sxs-lookup"><span data-stu-id="ad065-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ad065-111">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="ad065-111">Create an RCL</span></span>

<span data-ttu-id="ad065-112">Befolgen Sie die Anleitungen in <xref:blazor/get-started> diesem Artikel, um Ihre Umgebung für blazor zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ad065-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ad065-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad065-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ad065-114">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ad065-114">Create a new project.</span></span>
1. <span data-ttu-id="ad065-115">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="ad065-116">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ad065-116">Select **Next**.</span></span>
1. <span data-ttu-id="ad065-117">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="ad065-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ad065-118">In den Beispielen in diesem Thema wird der Projekt `MyComponentLib1`Name verwendet.</span><span class="sxs-lookup"><span data-stu-id="ad065-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="ad065-119">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-119">Select **Create**.</span></span>
1. <span data-ttu-id="ad065-120">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="ad065-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="ad065-121">Wählen Sie die Vorlage **Razor-Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="ad065-122">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-122">Select **Create**.</span></span>
1. <span data-ttu-id="ad065-123">Fügen Sie die RCL zu einer Projekt Mappe hinzu:</span><span class="sxs-lookup"><span data-stu-id="ad065-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="ad065-124">Klicken Sie mit der rechten Maustaste auf die Lösung.</span><span class="sxs-lookup"><span data-stu-id="ad065-124">Right-click the solution.</span></span> <span data-ttu-id="ad065-125">Wählen Sie**vorhandenes Projekt** **Hinzufügen** > .</span><span class="sxs-lookup"><span data-stu-id="ad065-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="ad065-126">Navigieren Sie zur Projektdatei der RCL.</span><span class="sxs-lookup"><span data-stu-id="ad065-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="ad065-127">Wählen Sie die Projektdatei ( *. csproj*) der RCL aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="ad065-128">Fügen Sie einen Verweis auf die RCL aus der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="ad065-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="ad065-129">Klicken Sie mit der rechten Maustaste auf das App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="ad065-129">Right-click the app project.</span></span> <span data-ttu-id="ad065-130">Wählen Sie**Verweis** **Hinzufügen** > aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="ad065-131">Wählen Sie das RCL-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="ad065-131">Select the RCL project.</span></span> <span data-ttu-id="ad065-132">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad065-132">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ad065-133">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ad065-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="ad065-134">Verwenden Sie die Vorlage für die Razor`razorclasslib`- **Klassenbibliothek** () mit dem Befehl [dotnet New](/dotnet/core/tools/dotnet-new) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="ad065-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="ad065-135">Im folgenden Beispiel wird eine RCL mit dem Namen `MyComponentLib1`erstellt.</span><span class="sxs-lookup"><span data-stu-id="ad065-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="ad065-136">Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="ad065-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="ad065-137">Um die Bibliothek einem vorhandenen Projekt hinzuzufügen, verwenden Sie den Befehl [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="ad065-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="ad065-138">Im folgenden Beispiel wird die RCL der app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ad065-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="ad065-139">Führen Sie den folgenden Befehl aus dem Projektordner der APP mit dem Pfad zur Bibliothek aus:</span><span class="sxs-lookup"><span data-stu-id="ad065-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="ad065-140">Rcls werden für Client seitige apps nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ad065-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="ad065-141">In der aktuellen ASP.net Core 3,0 Preview sind Razor-Klassenbibliotheken nicht mit Client seitigen blazor-Apps kompatibel.</span><span class="sxs-lookup"><span data-stu-id="ad065-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="ad065-142">Verwenden Sie für Client seitige blazor-apps eine blazor-Komponentenbibliothek, die `blazorlib` von der Vorlage in einer Befehlsshell erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="ad065-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="ad065-143">Komponenten Bibliotheken, die `blazorlib` die Vorlage verwenden, können statische Dateien enthalten, wie z. b. Bilder, JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="ad065-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="ad065-144">Zum Zeitpunkt der Erstellung werden statische Dateien in die integrierte Assemblydatei ( *. dll*) eingebettet, wodurch die Nutzung der Komponenten ermöglicht wird, ohne sich Gedanken über das einschließen ihrer Ressourcen machen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="ad065-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="ad065-145">Alle Dateien, die `content` im Verzeichnis enthalten sind, werden als eingebettete Ressource gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="ad065-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="ad065-146">Verwenden einer Bibliotheks Komponente</span><span class="sxs-lookup"><span data-stu-id="ad065-146">Consume a library component</span></span>

<span data-ttu-id="ad065-147">Verwenden Sie einen der folgenden Ansätze, um Komponenten zu verwenden, die in einer Bibliothek in einem anderen Projekt definiert sind:</span><span class="sxs-lookup"><span data-stu-id="ad065-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="ad065-148">Verwenden Sie den vollständigen Typnamen mit dem-Namespace.</span><span class="sxs-lookup"><span data-stu-id="ad065-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="ad065-149">Verwenden Sie die [ \@using](xref:mvc/views/razor#using) -Direktive von Razor.</span><span class="sxs-lookup"><span data-stu-id="ad065-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="ad065-150">Einzelne Komponenten können anhand des Namens hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="ad065-150">Individual components can be added by name.</span></span>

<span data-ttu-id="ad065-151">In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport` -Komponente enthält.</span><span class="sxs-lookup"><span data-stu-id="ad065-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="ad065-152">Auf `SalesReport` die Komponente kann mit dem vollständigen Typnamen mit Namespace verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="ad065-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="ad065-153">Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using` -Direktive in den Gültigkeitsbereich eingefügt wird:</span><span class="sxs-lookup"><span data-stu-id="ad065-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="ad065-154">Fügen Sie `@using MyComponentLib1` die-Direktive in die *_Import. Razor* -Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein gesamtes Projekt verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="ad065-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="ad065-155">Fügen Sie die-Direktive zu einer *_Import. Razor* -Datei auf jeder Ebene hinzu, um den Namespace auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="ad065-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="ad065-156">Erstellen, Verpacken und versenden an nuget</span><span class="sxs-lookup"><span data-stu-id="ad065-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="ad065-157">Da es sich bei Komponenten Bibliotheken um .net-Standardbibliotheken handelt, unterscheidet sich das Verpacken und Versenden von Dateien an nuget nicht von der Verpackung und dem Versand von Bibliotheken an nuget.</span><span class="sxs-lookup"><span data-stu-id="ad065-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="ad065-158">Die Paket Erstellung erfolgt mithilfe des Befehls " [dotnet Pack](/dotnet/core/tools/dotnet-pack) " in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="ad065-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="ad065-159">Laden Sie das Paket mit dem Befehl " [dotnet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) " in eine Befehlsshell in NuGet hoch:</span><span class="sxs-lookup"><span data-stu-id="ad065-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="ad065-160">Wenn Sie die `blazorlib` Vorlage verwenden, werden statische Ressourcen in das nuget-Paket eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ad065-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="ad065-161">Bibliotheks Consumer erhalten automatisch Skripts und Stylesheets, sodass Consumer die Ressourcen nicht manuell installieren müssen.</span><span class="sxs-lookup"><span data-stu-id="ad065-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="ad065-162">Erstellen einer Razor Components-Klassenbibliothek mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="ad065-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="ad065-163">Eine RCL kann statische Assets enthalten.</span><span class="sxs-lookup"><span data-stu-id="ad065-163">An RCL can include static assets.</span></span> <span data-ttu-id="ad065-164">Die statischen Ressourcen sind für jede app verfügbar, die die Bibliothek nutzt.</span><span class="sxs-lookup"><span data-stu-id="ad065-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="ad065-165">Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="ad065-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad065-166">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ad065-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>
