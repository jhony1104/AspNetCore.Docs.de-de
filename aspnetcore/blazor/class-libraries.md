---
title: ASP.net Core Razor Components-Klassenbibliotheken
author: guardrex
description: Erfahren Sie, wie Komponenten in Blazor-Apps aus einer externen Komponentenbibliothek eingeschlossen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 32088b43f91174596f6b9251d36782e806f966b9
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213248"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.net Core Razor Components-Klassenbibliotheken

Von [Simon Timms](https://github.com/stimms)

Komponenten können in einer Razor- [Klassenbibliothek (RCL)](xref:razor-pages/ui-class) Projekt übergreifend gemeinsam genutzt werden. Eine *Razor Components-Klassenbibliothek* kann in Folgendes eingeschlossen werden:

* Ein anderes Projekt in der Projekt Mappe.
* Ein nuget-Paket.
* Eine referenzierte .NET-Bibliothek.

Ebenso wie Komponenten reguläre .NET-Typen sind, sind von einer RCL bereitgestellte Komponenten normale .NET-Assemblys.

## <a name="create-an-rcl"></a>Erstellen einer RCL

Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um Ihre Umgebung für blazor zu konfigurieren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **Razor-Klassenbibliothek**aus. Wählen Sie **Weiter** aus.
1. Wählen Sie im Dialogfeld **neue Razor-Klassenbibliothek erstellen** die Option **Erstellen**aus.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. In den Beispielen in diesem Thema wird der Projektname `MyComponentLib1`verwendet. Klicken Sie auf **Erstellen**.
1. Fügen Sie die RCL zu einer Projekt Mappe hinzu:
   1. Klicken Sie mit der rechten Maustaste auf die Lösung. Wählen Sie **Hinzufügen** > **Vorhandenes Projekt** aus.
   1. Navigieren Sie zur Projektdatei der RCL.
   1. Wählen Sie die Projektdatei ( *. csproj*) der RCL aus.
1. Fügen Sie einen Verweis auf die RCL aus der APP hinzu:
   1. Klicken Sie mit der rechten Maustaste auf das App-Projekt. Wählen Sie > **Verweis** **Hinzufügen** aus.
   1. Wählen Sie das RCL-Projekt aus. Klicken Sie auf **OK**.

> [!NOTE]
> Wenn das Kontrollkästchen **Support Seiten und Ansichten** beim Generieren der RCL aus der Vorlage aktiviert ist, fügen Sie dem Stamm des generierten Projekts auch eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu, um die Erstellung von Razor-Komponenten zu aktivieren:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Fügen Sie dem Stamm des generierten Projekts manuell die Datei hinzu.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

1. Verwenden Sie die Vorlage für die **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [dotnet New](/dotnet/core/tools/dotnet-new) in einer Befehlsshell. Im folgenden Beispiel wird eine RCL namens `MyComponentLib1`erstellt. Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Wenn der `-s|--support-pages-and-views`-Schalter beim Generieren der RCL aus der Vorlage verwendet wird, fügen Sie dem Stamm des generierten Projekts auch eine *_Imports. Razor* -Datei mit folgendem Inhalt hinzu, um die Erstellung von Razor-Komponenten zu aktivieren:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Fügen Sie dem Stamm des generierten Projekts manuell die Datei hinzu.

1. Um die Bibliothek einem vorhandenen Projekt hinzuzufügen, verwenden Sie den Befehl [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell. Im folgenden Beispiel wird die RCL der app hinzugefügt. Führen Sie den folgenden Befehl aus dem Projektordner der APP mit dem Pfad zur Bibliothek aus:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Verwenden einer Bibliotheks Komponente

Verwenden Sie einen der folgenden Ansätze, um Komponenten zu verwenden, die in einer Bibliothek in einem anderen Projekt definiert sind:

* Verwenden Sie den vollständigen Typnamen mit dem-Namespace.
* Verwenden Sie die [\@using](xref:mvc/views/razor#using) -Direktive von Razor. Einzelne Komponenten können anhand des Namens hinzugefügt werden.

In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport` Komponente enthält.

Auf die `SalesReport` Komponente kann mit dem vollständigen Typnamen mit Namespace verwiesen werden:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using`-Direktive in den Gültigkeitsbereich eingebunden ist:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Fügen Sie die `@using MyComponentLib1`-Direktive in der *_Import. Razor* -Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein gesamtes Projekt verfügbar zu machen. Fügen Sie die-Direktive einer *_Import Razor* -Datei auf jeder Ebene hinzu, um den Namespace auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner anzuwenden.

## <a name="build-pack-and-ship-to-nuget"></a>Erstellen, Verpacken und versenden an nuget

Da es sich bei Komponenten Bibliotheken um .net-Standardbibliotheken handelt, unterscheidet sich das Verpacken und Versenden von Dateien an nuget nicht von der Verpackung und dem Versand von Bibliotheken an nuget. Die Paket Erstellung erfolgt mithilfe des Befehls " [dotnet Pack](/dotnet/core/tools/dotnet-pack) " in einer Befehlsshell:

```dotnetcli
dotnet pack
```

Laden Sie das Paket mit dem Befehl [dotnet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) in eine Befehlsshell in NuGet hoch.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Erstellen einer Razor Components-Klassenbibliothek mit statischen Assets

Eine RCL kann statische Assets enthalten. Die statischen Ressourcen sind für jede app verfügbar, die die Bibliothek nutzt. Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/ui-class>
