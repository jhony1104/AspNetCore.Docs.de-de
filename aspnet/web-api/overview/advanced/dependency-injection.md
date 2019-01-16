---
uid: web-api/overview/advanced/dependency-injection
title: Abhängigkeitsinjektion in ASP.NET Web-API 2 | Microsoft-Dokumentation
author: MikeWasson
description: In diesem Tutorial zeigt, wie Abhängigkeiten in Ihre ASP.NET Web-API-Controller eingefügt wird. Die Softwareversionen, die in dem Lernprogramm Web API 2 Unity-Anwendungsblock verwendet...
ms.author: riande
ms.date: 01/20/2014
ms.assetid: e3d3e7ba-87f0-4032-bdd3-31f3c1aa9d9c
msc.legacyurl: /web-api/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 318a2f1c587feb360212a390bb5de7bdc127513d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341825"
---
<a name="dependency-injection-in-aspnet-web-api-2"></a>Abhängigkeitsinjektion in ASP.NET Web-API 2
====================
durch [Mike Wasson](https://github.com/MikeWasson)

[Abgeschlossenes Projekt herunterladen](http://code.msdn.microsoft.com/ASP-NET-Web-API-Tutorial-468ee148)

> In diesem Tutorial zeigt, wie Abhängigkeiten in Ihre ASP.NET Web-API-Controller eingefügt wird.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
> 
> 
> - Web-API 2
> - [Unity-Anwendungsblock](https://www.nuget.org/packages/Unity/)
> - Entity Framework 6 (Version 5 funktioniert auch)


## <a name="what-is-dependency-injection"></a>Was ist Dependency Injection?

Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt. Es ist beispielsweise üblich, definieren Sie eine [Repository](http://martinfowler.com/eaaCatalog/repository.html) , die den Datenzugriff verarbeitet. Lassen Sie uns mit einem Beispiel veranschaulichen. Zuerst definieren wir ein Domänenmodell:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

Hier ist eine einfache repositoryklasse, die Elemente in einer Datenbank mithilfe von Entity Framework speichert.

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Jetzt definieren wir einen Web-API-Controller, der GET-Anforderungen für unterstützt `Product` Entitäten. (Ich bin, POST und andere Methoden aus Gründen der Einfachheit vorgenommen werden müssen.) Hier ist ein erster Versuch:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Beachten Sie, von denen die Controller-Klasse abhängt `ProductRepository`, und wir werden den Controller erstellen lassen die `ProductRepository` Instanz. Allerdings ist es eine schlechte Idee, hartcodieren der Abhängigkeit auf diese Weise können verschiedene Ursachen haben.

- Wenn Sie ersetzen möchten `ProductRepository` durch eine andere Implementierung verwenden, müssen Sie auch die Controllerklasse ändern.
- Wenn die `ProductRepository` über Abhängigkeiten verfügt, müssen Sie diese in den Controller konfigurieren. Für ein großes Projekt mit mehreren Controllern wird der Konfigurationscode auf Ihr Projekt verteilt.
- Es ist schwierig, Komponententests, da der Controller für die Datenbank abzufragen hartcodiert ist. Für einen Komponententest sollten Sie ein Mock oder Stub-Repository, verwenden, die nicht mit dem Entwurf Benutzerverzeichnis möglich ist.

Wir können diese Probleme durch adressieren *einfügen* das Repository in den Controller. Gestalten Sie zunächst die `ProductRepository` Klasse in eine Schnittstelle:

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Geben Sie dann die `IProductRepository` als Konstruktorparameter:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Dieses Beispiel verwendet [Konstruktorinjektion](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Sie können auch *Setter-Injektion*, in dem Sie die Abhängigkeit über einen Setter-Methode oder Eigenschaft festlegen.

Aber jetzt gibt es kein Problem, da Ihre Anwendung nicht für den Controller direkt erstellt werden. Web-API erstellt den Controller aus, wenn die Anforderung leitet, und die Web-API weiß nicht, alles zu `IProductRepository`. Dies kommt der Web-API-Abhängigkeitskonfliktlöser.

## <a name="the-web-api-dependency-resolver"></a>Der Abhängigkeitskonfliktlöser-Web-API

Web-API definiert die **IDependencyResolver** Schnittstelle zum Auflösen von Abhängigkeiten. Hier ist die Definition der Schnittstelle:

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

Die **IDependencyScope** Schnittstelle verfügt über zwei Methoden:

- **"GetService"** erstellt eine Instanz eines Typs.
- **GetServices** erstellt eine Auflistung von Objekten eines angegebenen Typs.

Die **IDependencyResolver** Methode erbt **IDependencyScope** und fügt die **BeginScope** Methode. Ich werde später in diesem Tutorial Informationen zu Bereichen.

Wenn die Web-API-Controller-Instanz erstellt wird, ruft er zuerst **IDependencyResolver.GetService**, und übergeben Sie den Typ des Domänencontrollers. Sie können dieser erweiterungsmöglichkeit verwenden, im Controller zu erstellen, alle Abhängigkeiten werden aufgelöst. Wenn **"GetService"** gibt null zurück, Web-API, sucht einen parameterlosen Konstruktor für die Controllerklasse.

## <a name="dependency-resolution-with-the-unity-container"></a>Abhängigkeitsauflösung mit den Unity-Container

Obwohl Sie eine vollständige schreiben könnten **IDependencyResolver** Implementierung von Grund auf, die Schnittstelle ist speziell, fungiert als Brücke zwischen Web-API und vorhandenen IoC-Container.

Ein IoC-Container ist eine Softwarekomponente, die zum Verwalten von Abhängigkeiten zuständig ist. Typen mit dem Container registrieren, und klicken Sie dann den Container verwenden, um Objekte zu erstellen. Der Container ermittelt automatisch die Abhängigkeit Beziehungen. Viele IoC-Container können auch steuern, z. B. Objektlebensdauer und Bereich.

> [!NOTE]
> "IoC" steht für "Inversion of Control", dies ist ein allgemeines Muster, in denen ein Framework in Anwendungscode aufruft. Ein IoC-Container erstellt die Objekte, die "die übliche ablaufsteuerung kehrt".


In diesem Tutorial verwenden wir [Unity](https://msdn.microsoft.com/library/ff647202.aspx) aus Microsoft Patterns &amp; Methoden. (Andere beliebten Bibliotheken enthalten [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Ninject](http://www.ninject.org/), und [StructureMap ](http://structuremap.github.io/documentation/).) Sie können NuGet-Paket-Manager verwenden, zum Installieren von Unity. Aus der **Tools** in Visual Studio, wählen Sie im Menü **NuGet Paket-Manager**, dann **Paket-Manager Konsole**. Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl ein:

[!code-console[Main](dependency-injection/samples/sample7.cmd)]

Hier ist eine Implementierung von **IDependencyResolver** , ein Unity-Containers einschließt.

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

> [!NOTE]
> Wenn die **"GetService"** Methode ein Typs kann nicht aufgelöst werden, sollte zurückgegeben **null**. Wenn die **GetServices** Methode ein Typs kann nicht aufgelöst werden, sollten sie ein leeres Auflistungsobjekt zurück. Ausnahmen Sie keine für unbekannte Typen.


## <a name="configuring-the-dependency-resolver"></a>Konfigurieren den Abhängigkeitskonfliktlöser

Legen Sie den Abhängigkeitskonfliktlöser für die **DependencyResolver** Eigenschaft des globalen **HttpConfiguration** Objekt.

Der folgende code registriert das `IProductRepository` sind über Schnittstellen mit Unity und erstellt dann eine `UnityResolver`.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

## <a name="dependency-scope-and-controller-lifetime"></a>Abhängigkeitsbereich und Controller-Lebensdauer

Controller werden pro Anforderung erstellt. Zum Verwalten der Lebensdauer von Objekten, **IDependencyResolver** verwendet das Konzept einer *Bereich*.

Der Abhängigkeitskonfliktlöser angefügt, um die **HttpConfiguration** Objekt hat einen globalen Gültigkeitsbereich. Wenn die Web-API einen Controller erstellt, ruft er **BeginScope**. Diese Methode gibt ein **IDependencyScope** , einen untergeordneten Bereich darstellt.

Web-API ruft dann **"GetService"** im untergeordneten Bereich den Controller zu erstellen. Wenn die Anforderung abgeschlossen ist, Web-API-Aufrufe **Dispose** für den untergeordneten Bereich. Verwenden der **Dispose** Methode, der Abhängigkeiten des Controllers zu verwerfen.

Implementierung der **BeginScope** hängt von den IoC-Container. Für Unity entspricht einem untergeordneten Container Bereich:

[!code-csharp[Main](dependency-injection/samples/sample10.cs)]

Die meisten IoC-Containern haben ähnliche Entsprechungen.
