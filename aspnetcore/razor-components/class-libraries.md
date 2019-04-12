---
title: Klassenbibliotheken für Razor-Komponenten
author: guardrex
description: Ermitteln Sie, wie die Komponenten in Razor-Komponenten-apps aus einer externen Komponentenbibliothek aufgenommen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515461"
---
# <a name="razor-components-class-libraries"></a>Klassenbibliotheken für Razor-Komponenten

Durch [Simon Timms](https://github.com/stimms)

Komponenten können in Razor-Klassenbibliotheken für Projekte gemeinsam genutzt werden. Komponenten können aus berücksichtigt werden:

* Ein anderes Projekt in der Projektmappe.
* Ein NuGet-Paket.
* Eine Bibliothek mit .NET auf die verwiesen wird.

Genau, wie Komponenten reguläre .NET Typen sind, sind Komponenten, die durch Razor-Klassenbibliotheken bereitgestellt normale .NET Assemblys.

Verwenden der `razorclasslib` (Razor) Klassenbibliotheksvorlage mit der [Dotnet neue](/dotnet/core/tools/dotnet-new) Befehl:

```console
dotnet new razorclasslib -o MyComponentLib1
```

Hinzufügen von Dateien mit Razor-Komponente (*.razor*) auf die Razor-Klassenbibliothek.

Um die Bibliothek zu einem vorhandenen Projekt hinzuzufügen, verwenden die [Dotnet-Sln](/dotnet/core/tools/dotnet-sln) Befehl:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> Razor-Klassenbibliotheken nicht kompatibel mit Blazor-apps in ASP.NET Core Preview 3.
>
> Verwenden Sie zum Erstellen von Komponenten in einer Bibliothek, die mit Blazor und Razor-Komponenten-apps freigegeben werden können, eine Klassenbibliothek Blazor erstellt die `blazorlib` Vorlage.
>
> Razor-Klassenbibliotheken unterstützt nicht statische Ressourcen in ASP.NET Core Preview 3. Komponentenbibliotheken, die mit der `blazorlib` Vorlage kann statische Dateien, z. B. Bilder, JavaScript und Stylesheets enthalten. Zur Buildzeit werden statische Dateien in die erstellte Assembly-Datei eingebettet (*DLL*), der Verbrauch der Komponenten ermöglicht, ohne dazu, wie Sie ihre Ressourcen enthalten. Alle Dateien in die `content` Verzeichnis als eingebettete Ressource gekennzeichnet sind.

## <a name="consume-a-library-component"></a>Nutzen Sie eine Bibliothekskomponente

Um in einer Bibliothek in einem anderen Projekt definierten Komponenten nutzen den [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) Richtlinie muss verwendet werden. Einzelne Komponenten können anhand des Namens hinzugefügt werden.

Das allgemeine Format für die Direktive ist:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Die folgende Anweisung fügt beispielsweise `Component1` von `MyComponentLib1`:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

Es ist jedoch üblich, alle Komponenten aus einer Assembly mit einem Platzhalter enthalten (`*`):

```cshtml
@addTagHelper *, MyComponentLib1
```

Die `@addTagHelper` Richtlinie enthalten sein kann, im *_ViewImport.cshtml* die Komponenten verfügbar, die für ein gesamtes Projekt oder angewendet auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner vornehmen. Mit der `@addTagHelper` Direktive vorhanden, die Bestandteile der Komponentenbibliothek genutzt werden können als wären sie in der gleichen Assembly wie die app.

## <a name="build-pack-and-ship-to-nuget"></a>Build, Pack und Versand zu NuGet

Da Komponentenbibliotheken .NET Standardbibliotheken sind, unterscheidet Verpackung und Lieferung an den NuGet sich nicht von der Verpackung und Versand einer Bibliothek auf NuGet. Paketerstellung erfolgt unter Verwendung der [Dotnet-Pack](/dotnet/core/tools/dotnet-pack) Befehl:

```console
dotnet pack
```

Hochladen des Pakets mit NuGet die [Dotnet Nuget veröffentlichen](/dotnet/core/tools/dotnet-nuget-push) Befehl:

```console
dotnet nuget publish
```

Bei Verwendung der `blazorlib` Vorlage statische Ressourcen in das NuGet-Paket enthalten sind. Consumer erhalten automatisch Skripts und Stylesheets, damit der Consumer nicht erforderlich, um die Ressourcen manuell zu installieren sind.
