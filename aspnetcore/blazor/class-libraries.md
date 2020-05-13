---
title: Klassenbibliotheken für ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten aus einer externen Komponentenbibliothek in Blazor-Apps einbezogen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 57f3494fd825b6549c40f56962da2c8076e8fd51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767095"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>Klassenbibliotheken für ASP.NET Core-Razor-Komponenten

Von [Simon Timms](https://github.com/stimms)

Komponenten können in einer [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) projektübergreifend gemeinsam genutzt werden. Eine *Klassenbibliothek für Razor-Komponenten* kann wie folgt einbezogen werden:

* Über ein anderes Projekt in der Projektmappe.
* Über ein NuGet-Paket.
* Über eine referenzierte .NET-Bibliothek.

Genauso wie Komponenten normale .NET-Typen sind, sind von der RCL bereitgestellte Komponenten normale .NET-Assemblys.

## <a name="create-an-rcl"></a>Erstellen einer RCL

Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um Ihre Umgebung für Blazor zu konfigurieren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **Razor-Klassenbibliothek** aus. Klicken Sie auf **Weiter**.
1. Wählen Sie im Dialogfeld **Neue Razor-Klassenbibliothek erstellen** die Option **Erstellen** aus.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Die Beispiele in diesem Thema verwenden den Projektnamen `MyComponentLib1`. Wählen Sie **Erstellen** aus.
1. Fügen Sie die RCL zu einer Projektmappe hinzu:
   1. Klicken Sie mit der rechten Maustaste auf die Projektmappe. Wählen Sie **Hinzufügen** > **Vorhandenes Projekt** aus.
   1. Navigieren Sie zur Projektdatei der RCL.
   1. Wählen Sie die Projektdatei (*CSPROJ*) der RCL aus.
1. Fügen Sie einen Verweis auf die RCL aus der App hinzu:
   1. Klicken Sie mit der rechten Maustaste auf das App-Projekt. Wählen Sie **Hinzufügen** > **Verweis** aus.
   1. Wählen Sie das RCL-Projekt aus. Klicken Sie auf **OK**.

> [!NOTE]
> Wenn das Kontrollkästchen **Seiten und Ansichten unterstützen** beim Erstellen der RCL aus der Vorlage aktiviert ist, fügen Sie auch eine *_Imports.razor*-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

1. Verwenden Sie die Vorlage **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) in einer Befehlsshell. Im folgenden Beispiel wird eine RCL mit dem Namen `MyComponentLib1` erstellt. Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Wenn der Switch `-s|--support-pages-and-views` beim Erstellen der RCL aus der Vorlage verwendet wird, fügen Sie auch eine *_Imports.razor*-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.

1. Verwenden Sie den Befehl [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell, um die Bibliothek zu einem bestehenden Projekt hinzuzufügen. Im folgenden Beispiel wird die RCL der App hinzugefügt. Führen Sie den folgenden Befehl aus dem Projektordner der App mit dem Pfad zur Bibliothek aus:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Verwenden einer Bibliothekskomponente

Um Komponenten, die in einer Bibliothek definiert sind, in einem anderen Projekt zu verwenden, verwenden Sie einen der folgenden Ansätze:

* Verwenden Sie den vollständigen Typnamen mit dem Namespace.
* Verwenden Sie die [\@using](xref:mvc/views/razor#using)-Anweisung von Razor. Einzelne Komponenten können über den Namen hinzugefügt werden.

In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport`-Komponente enthält.

Die `SalesReport`-Komponente kann über ihren vollständigen Typnamen mit Namespace referenziert werden:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using`-Direktive in den Gültigkeitsbereich eingebunden wird:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Fügen Sie die `@using MyComponentLib1`-Direktive in die *_Import.razor*-Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein ganzes Projekt zur Verfügung zu stellen. Fügen Sie die Direktive auf beliebiger Ebene zu einer *_Import.razor*-Datei hinzu, um den Namespace auf eine einzelne Seite oder eine Reihe von Seiten innerhalb eines Ordners anzuwenden.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Erstellen einer Klassenbibliothek für Razor-Komponenten mit statischen Objekten

Eine RCL kann statische Objekte enthalten. Die statischen Objekte sind für jede App verfügbar, die die Bibliothek nutzt. Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Erstellen, Verpacken und Liefern an NuGet

Da es sich bei Komponentenbibliotheken um standardmäßige .NET-Bibliotheken handelt, unterscheidet sich das Verpacken und Liefern an NuGet nicht vom Verpacken und Liefern einer beliebigen Bibliothek an NuGet. Das Verpacken wird mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack) in einer Befehlsshell durchgeführt:

```dotnetcli
dotnet pack
```

Laden Sie das Paket in NuGet mit dem Befehl [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) in einer Befehlsshell hoch.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/ui-class>
* [Hinzufügen einer XML-Linkerkonfigurationsdatei zu einer Bibliothek](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
