---
title: ASP.net Core Razor Components-Klassenbibliotheken
author: guardrex
description: Erfahren Sie, wie Komponenten in blazor-Apps aus einer externen Komponentenbibliothek eingeschlossen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/class-libraries
ms.openlocfilehash: d9ef276357e95d97b7d89427c5e237aceea7a0d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207102"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.net Core Razor Components-Klassenbibliotheken

Von [Simon Timms](https://github.com/stimms)

Komponenten können in einer Razor- [Klassenbibliothek (RCL)](xref:razor-pages/ui-class) Projekt übergreifend gemeinsam genutzt werden. Eine *Razor Components-Klassenbibliothek* kann in Folgendes eingeschlossen werden:

* Ein anderes Projekt in der Projekt Mappe.
* Ein nuget-Paket.
* Eine referenzierte .NET-Bibliothek.

Ebenso wie Komponenten reguläre .NET-Typen sind, sind von einer RCL bereitgestellte Komponenten normale .NET-Assemblys.

## <a name="create-an-rcl"></a>Erstellen einer RCL

Befolgen Sie die Anleitungen in <xref:blazor/get-started> diesem Artikel, um Ihre Umgebung für blazor zu konfigurieren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **Razor-Klassenbibliothek**aus. Klicken Sie auf **Weiter**.
1. Wählen Sie im Dialogfeld **neue Razor-Klassenbibliothek erstellen** die Option **Erstellen**aus.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. In den Beispielen in diesem Thema wird der Projekt `MyComponentLib1`Name verwendet. Wählen Sie **Erstellen** aus.
1. Fügen Sie die RCL zu einer Projekt Mappe hinzu:
   1. Klicken Sie mit der rechten Maustaste auf die Lösung. Wählen Sie**vorhandenes Projekt** **Hinzufügen** > .
   1. Navigieren Sie zur Projektdatei der RCL.
   1. Wählen Sie die Projektdatei ( *. csproj*) der RCL aus.
1. Fügen Sie einen Verweis auf die RCL aus der APP hinzu:
   1. Klicken Sie mit der rechten Maustaste auf das App-Projekt. Wählen Sie**Verweis** **Hinzufügen** > aus.
   1. Wählen Sie das RCL-Projekt aus. Klicken Sie auf **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

1. Verwenden Sie die Vorlage für die Razor`razorclasslib`- **Klassenbibliothek** () mit dem Befehl [dotnet New](/dotnet/core/tools/dotnet-new) in einer Befehlsshell. Im folgenden Beispiel wird eine RCL mit dem Namen `MyComponentLib1`erstellt. Der Ordner, der `MyComponentLib1` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Um die Bibliothek einem vorhandenen Projekt hinzuzufügen, verwenden Sie den Befehl [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell. Im folgenden Beispiel wird die RCL der app hinzugefügt. Führen Sie den folgenden Befehl aus dem Projektordner der APP mit dem Pfad zur Bibliothek aus:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Verwenden einer Bibliotheks Komponente

Verwenden Sie einen der folgenden Ansätze, um Komponenten zu verwenden, die in einer Bibliothek in einem anderen Projekt definiert sind:

* Verwenden Sie den vollständigen Typnamen mit dem-Namespace.
* Verwenden Sie die [ \@using](xref:mvc/views/razor#using) -Direktive von Razor. Einzelne Komponenten können anhand des Namens hinzugefügt werden.

In den folgenden Beispielen ist `MyComponentLib1` eine Komponentenbibliothek, die eine `SalesReport` -Komponente enthält.

Auf `SalesReport` die Komponente kann mit dem vollständigen Typnamen mit Namespace verwiesen werden:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Auf die Komponente kann auch verwiesen werden, wenn die Bibliothek mit einer `@using` -Direktive in den Gültigkeitsbereich eingefügt wird:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Fügen Sie `@using MyComponentLib1` die-Direktive in die *_Import. Razor* -Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein gesamtes Projekt verfügbar zu machen. Fügen Sie die-Direktive zu einer *_Import. Razor* -Datei auf jeder Ebene hinzu, um den Namespace auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner anzuwenden.

## <a name="build-pack-and-ship-to-nuget"></a>Erstellen, Verpacken und versenden an nuget

Da es sich bei Komponenten Bibliotheken um .net-Standardbibliotheken handelt, unterscheidet sich das Verpacken und Versenden von Dateien an nuget nicht von der Verpackung und dem Versand von Bibliotheken an nuget. Die Paket Erstellung erfolgt mithilfe des Befehls " [dotnet Pack](/dotnet/core/tools/dotnet-pack) " in einer Befehlsshell:

```dotnetcli
dotnet pack
```

Laden Sie das Paket mit dem Befehl " [dotnet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) " in eine Befehlsshell in NuGet hoch:

```dotnetcli
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Erstellen einer Razor Components-Klassenbibliothek mit statischen Assets

Eine RCL kann statische Assets enthalten. Die statischen Ressourcen sind für jede app verfügbar, die die Bibliothek nutzt. Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/ui-class>
