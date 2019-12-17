---
title: Anpassen von Modellbindungen in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr darüber, wie Controlleraktionen durch Modellbindungen direkt mit den Modelltypen in ASP.NET Core arbeiten.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 625cc6c9ca5a2c22d028ea25f8fc0d942b71f12d
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881132"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Anpassen von Modellbindungen in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten. Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt. Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/).

## <a name="default-model-binder-limitations"></a>Standardmodellbindungseinschränkungen

Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler. Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können. Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können. Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann. Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.

## <a name="model-binding-review"></a>Übersicht: Modellbindung

Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet. Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert. Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert. Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts. Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.

Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden. Betrachten Sie [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder), das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren. Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.

### <a name="working-with-the-bytearraymodelbinder"></a>Arbeiten mit ByteArrayModelBinder

Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen. Das folgende Bild kann beispielsweise als Zeichenfolge codiert werden:

![Dotnet-Bot](custom-model-binding/images/bot.png "Dotnet-Bot")

Ein kleiner Anteil der codierten Zeichenfolge wird in der folgenden Abbildung dargestellt:

![Dotnet-Bot, codiert](custom-model-binding/images/encoded-bot.png "Dotnet-Bot, codiert")

Befolgen Sie die Anweisungen in der [README-Datei des Beispiels](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md), um die Base64-codierte Zeichenfolge in eine Datei zu konvertieren.

ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren. [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider), der [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) implementiert, ordnet `byte[]`-Argumente `ByteArrayModelBinder` zu:

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

Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute) verwenden.

In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:

![Postman](custom-model-binding/images/postman.png "Postman")

Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich. Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Beispiel für eine benutzerdefinierte Modellbindung

In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:

- Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente
- Abrufen der verknüpften Entität mit Entity Framework Core
- Übergeben der verknüpften Entität als Argument an die Aktionsmethode

In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.

Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen. Die Klasse stellt keine bewährte Methode für ein Suchszenario dar. Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab. Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.

Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben. Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht. Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben. Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.

Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.

### <a name="implementing-a-modelbinderprovider"></a>Implementieren von ModelBinderProvider

Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren. So werden die integrierten Frameworkbindungen implementiert. Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert. Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`. Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Hinweis: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück. `BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI). `AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann. Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.

Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen. Der erste Anbieter, der eine Bindung zurückgibt, wird verwendet.

In der folgenden Abbildung werden die Standardmodellbindungen des Debuggers gezeigt:

![Standardmodellbindungen](custom-model-binding/images/default-model-binders.png "Standardmodellbindungen")

Wenn Sie Ihren Anbieter am Ende der Auflistung hinzufügen, kann es passieren, dass ein integrierter Modellbindung aufgerufen wird, bevor Ihre benutzerdefinierte Bindung an die Reihe kommt. In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er auch tatsächlich für `Author`-Aktionsargumente verwendet wird.

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a>Polymorphe Modellbindung

Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet. Eine benutzerdefinierte polymorphe Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss. Polymorphe Modellbindung:

* Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.
* Erschwert es, Informationen über die gebundenen Modelle zu bekommen.

Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Empfehlungen und bewährte Methoden

Benutzerdefinierte Modellbindungen:

- Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden). Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.
- Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.
- Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. Ein [TypeConverter](/dotnet/api/system.componentmodel.typeconverter) ist meist die sinnvollere Option.
