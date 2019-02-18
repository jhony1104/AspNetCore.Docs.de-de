---
title: Modellvalidierung im ASP.NET Core MVC
author: tdykstra
description: Informationen zur Modellvalidierung im ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: mvc/models/validation
ms.openlocfilehash: ca7ee54b8e6b6ae5091b0cb133e448ad9c04da8f
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248518"
---
# <a name="model-validation-in-aspnet-core-mvc"></a>Modellvalidierung im ASP.NET Core MVC

Von [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>Einführung in die Modellvalidierung

Bevor eine App Daten in einer Datenbank speichern kann, muss sie diese Daten überprüfen. Daten müssen auf mögliche Sicherheitsbedrohungen überprüft werden. Außerdem muss geprüft werden, ob sie richtig nach Typ und Größe formatiert und mit den Regeln konform sind. Die Validierung kann zwar redundant und die Implementierung aufwendig sein, jedoch ist sie unbedingt notwendig. Im MVC werden sowohl der Client als auch der Server validiert.

Praktischerweise hat .NET die Validierung in Validierungsattribute unterteilt. Diese Attribute enthalten Validierungscode, sodass Sie weniger Code schreiben müssen.

In ASP.NET Core 2.2 und höher umgeht (überspringt) die ASP.NET Core-Runtime die Validierung, wenn sie bestimmen kann, dass ein vorgegebener Modellgraph keine Validierung erfordert. Das Überspringen der Validierung kann zu erheblichen Leistungsverbesserungen führen, wenn die zu validierenden Modelle keine Validierungssteuerelemente verwenden können oder ihnen keine Validierungssteuerelemente zugeordnet sind. Die übersprungene Validierung umfasst Objekte wie z.B. Sammlungen von primitiven (`byte[]`, `string[]`, `Dictionary<string, string>` usw.) oder komplexen Objektgraphen ohne Validierungssteuerelemente.

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).

## <a name="validation-attributes"></a>Validierungsattribute

Mithilfe von Validierungsattributen können Sie die Modellvalidierung konfigurieren, sodass sie dem Konzept zum Validieren von Feldern in Datenbanktabellen ähnelt. Dies beinhaltet Einschränkungen wie das Zuweisen von Datentypen oder erforderlichen Feldern. Andere Typvalidierungen umfassen die Anwendung von Mustern auf Daten, um Geschäftsregeln zu erzwingen – z.B. das Angeben einer Kreditkarte, einer Telefonnummer oder einer E-Mail-Adresse. Mithilfe von Validierungsattributen können Anforderungen viel einfacher erzwungen werden.

Validierungsattribute werden auf der Eigenschaftenebene festgelegt: 

```csharp
[Required]
public string MyProperty { get; set; }
```

Nachfolgend finden Sie ein annotiertes `Movie`-Modell einer App, das Informationen über Filme und Fernsehserien speichert. Die meisten Eigenschaften sind erforderlich und einige Zeichenfolgeneigenschaften haben Längenanforderungen. Außerdem gibt es neben einem benutzerdefinierten Validierungsattribut auch eine Bereichseinschränkung für die `Price`-Eigenschaft von 0 (null) bis 999,99 $.

[!code-csharp[](validation/sample/Movie.cs?range=6-29)]

Wenn Sie das Modell ansehen, erfahren Sie, welche Regeln über Daten für diese App gelten, wodurch Sie den Code einfacher verwalten können. Im Folgenden finden Sie verschiedene beliebte integrierte Validierungsattribute:

* `[CreditCard]`: Überprüft, ob die Eigenschaft über ein Kreditkartenformat verfügt.

* `[Compare]`: Überprüft, ob zwei Eigenschaften in einem Modell miteinander übereinstimmen.

* `[EmailAddress]`: Überprüft, ob die Eigenschaft über ein E-Mail-Format verfügt.

* `[Phone]`: Überprüft, ob die Eigenschaft über ein Telefonformat verfügt.

* `[Range]`: Überprüft, ob der Eigenschaftenwert im vorgegebenen Bereich liegt.

* `[RegularExpression]`: Überprüft, ob die Daten mit dem angegebenen regulären Ausdruck übereinstimmt.

* `[Required]`: Legt eine Eigenschaft als erforderlich fest.

* `[StringLength]`: Überprüft, ob eine Zeichenfolgeneigenschaft die maximale Länge nicht überschreitet.

* `[Url]`: Überprüft, ob die Eigenschaft über ein URL-Format verfügt.

MVC unterstützt alle Attribute, die von `ValidationAttribute` zu Validierungszwecken abgeleitet werden. Im [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)-Namespace finden Sie viele nützliche Validierungsattribute.

Für manche Instanzen benötigen Sie ggf. mehr Features als die integrierten Attribute bereitstellen. Wenn dies der Fall ist, können Sie benutzerdefinierte Validierungsattribute erstellen, indem Sie von `ValidationAttribute` ableiten oder Ihr Modell verändern, sodass es `IValidatableObject` implementiert.

## <a name="notes-on-the-use-of-the-required-attribute"></a>Hinweise zur Verwendung des erforderlichen Attributs

[Werttypen](/dotnet/csharp/language-reference/keywords/value-types), bei denen es sich nicht um Nullable-Typen handelt (wie z.B. `decimal`, `int`, `float` und `DateTime`), sind grundsätzlich erforderlich und benötigen das Attribut `Required` nicht. Die App führt keine Validierungsüberprüfungen auf Serverseite für nicht auf NULL festlegbare Typen aus, die als `Required` markiert sind.

Die MVC-Modellbindung, die bei der Validierung und bei Validierungsattributen nicht in Betracht gezogen wird, lehnt die Übertragung eines Formularfelds ab, die einen fehlenden Wert oder einen Platzhalter für einen nicht auf NULL festlegbaren Typ enthält. Wenn kein `BindRequired`-Attribut für die Zieleigenschaft vorhanden ist, ignoriert die Modellbindung die fehlenden Daten für nicht auf NULL festlegbare Typen, wenn das Formularfeld keine eingehenden Daten enthält.

Das [BindRequired-Attribut](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (siehe <xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>) ist nützlich, um sicherzustellen, dass die Formulardaten vollständig sind. Wenn das Modellbindungssystem auf eine Eigenschaft angewendet wird, ist ein Wert für diese Eigenschaft erforderlich. Wenn das Modellbindungssystem auf einen Typ angewendet wird, sind für alle Eigenschaften dieses Typs Werte erforderlich.

Wenn Sie einen [Nullable\<T>-Typ](/dotnet/csharp/programming-guide/nullable-types/) (z.B. `decimal?` oder `System.Nullable<decimal>`) verwenden, und ihn als `Required` markieren, wird eine Validierungsüberprüfung auf Serverseite ausgeführt. Dabei wird angenommen, dass es sich bei der Eigenschaft um einen Nullable-Standardtyp (z.B. eine `string`) handelt.

Für die Validierung auf Clientseite ist ein Wert für ein Formularfeld, das mit einer Modelleigenschaft übereinstimmt, die Sie als `Required` markiert haben, und für eine nicht auf NULL festlegbare Typeigenschaft erforderlich, die Sie nicht als `Required` markiert haben. `Required` kann verwendet werden, um die Fehlermeldung der Validierung auf Clientseite zu kontrollieren.

::: moniker range=">= aspnetcore-2.1"

## <a name="top-level-node-validation"></a>Validierung der Knoten auf oberster Ebene

Knoten der obersten Ebene sind unter anderem:

* Aktionsparameter
* Controllereigenschaften
* Seitenhandlerparameter
* Seitenmodelleigenschaften

An ein Modell gebundene Knoten auf oberster Ebene und Modelleigenschaften werden überprüft. Im folgenden Beispiel aus der Beispiel-App verwendet die `VerifyPhone`-Methode die <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>-Klasse, um die Benutzerdaten im Telefonfeld eines Formulars zu überprüfen

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyPhone)]

Knoten auf oberster Ebene können <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> mit Validierungsattributen verwenden. Im folgenden Beispiel aus der Beispiel-App gibt die `CheckAge`-Methode an, dass der `age`-Parameter aus der Abfragezeichenfolge gebunden sein muss, wenn das Formular übermittelt wird:

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_CheckAge)]

Auf der Seite für die Altersprüfung (*CheckAge.cshtml*) sind zwei Formulare vorhanden. Das erste Formular übermittelt einen `Age`-Wert von `99` als Abfragezeichenfolge: `https://localhost:5001/Users/CheckAge?Age=99`.

Wenn ein ordnungsgemäß formatierter `age`-Parameter aus der Abfragezeichenfolge übermittelt wird, wird das Formular überprüft.

Das zweite Formular auf der Seite für die Altersprüfung übermittelt den `Age`-Wert im Anforderungstext, worauf die Validierung fehlschlägt. Die Bindung schlägt fehl, da der `age`-Parameter aus einer Abfragezeichenfolge stammen muss.

Die Validierung wird standardmäßig von der Eigenschaft <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> von <xref:Microsoft.AspNetCore.Mvc.MvcOptions> aktiviert und gesteuert. Legen Sie in den MVC-Optionen (`Startup.ConfigureServices`) `AllowValidatingTopLevelNodes` auf `false` fest, um die Validierung der Knoten auf oberster Ebene zu deaktivieren.

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

::: moniker-end

## <a name="model-state"></a>Modellstatus

Der Modellstatus stellt Validierungsfehler in übermittelten HTML-Formularwerten dar.

MVC überprüft die Felder solange, bis die maximale Anzahl von Fehlern (standardmäßig 200) erreicht wird. Sie können diese Zahl mit dem folgenden Code in `Startup.ConfigureServices` konfigurieren:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors)]

## <a name="handle-model-state-errors"></a>Behandeln von Modellstatusfehlern

Die Modellprüfung erfolgt vor der Ausführung einer Controlleraktion. Die Überprüfung von `ModelState.IsValid` und entsprechende Maßnahmen erfolgen im Rahmen der Aktion. Als angemessene Reaktion gilt in der Regel die Rückgabe einer Fehlerantwort, in der bestenfalls der Grund angegeben wird, warum die Modellvalidierung fehlgeschlagen ist.

::: moniker range=">= aspnetcore-2.1"

Wenn `ModelState.IsValid` in Web-API-Controllern mit dem Attribut `[ApiController]` `false` ergibt, wird eine automatische HTTP 400-Antwort mit Details zum Problem zurückgegeben. Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).

::: moniker-end

Einige Apps wählen eine Standardkonvention aus, um Modellvalidierungsfehler zu behandeln. Dann kann ein Filter hilfreich sein, um eine solche Richtlinie zu implementieren. Sie sollten testen, wie sich Ihre Aktionen mit gültigen und ungültigen Modellstatus verhalten.

## <a name="manual-validation"></a>Manuelle Validierung

Sobald die Modellbindung und die Validierung abgeschlossen sind, sollten Sie einzelne Bestandteile dieser beiden Vorgänge wiederholen. Es kann z.B. sein, dass ein Benutzer Text in ein Feld eingegeben hat und einen Integer erwartet, oder dass Sie einen Wert für eine Eigenschaft des Modells berechnen müssen.

Möglicherweise müssen Sie die Validierung manuell ausführen. Rufen Sie dazu wie im Folgenden dargestellt die `TryValidateModel`-Methode auf:

[!code-csharp[](validation/sample/MoviesController.cs?name=snippet_TryValidateModel)]

## <a name="custom-validation"></a>Benutzerdefinierte Validierung

Validierungsattribute funktionieren für die meisten Anforderungen, die im Zusammenhang mit der Validierung stehen. Manche Validierungsregeln gelten nur für Ihr Unternehmen. Möglicherweise handelt es sich bei Ihren Regeln nicht um häufig verwendete Techniken zur Datenvalidierung wie die Gewährleistung, dass ein Feld erforderlich ist oder dass das Feld mit einem Wertebereich konform ist. In diesen Szenarios bieten sich Validierungsattribute als Lösung an. Das Erstellen Ihrer eigenen benutzerdefinierten Validierungsattribute in MVC ist einfach. Erben Sie einfach von dem `ValidationAttribute`-Attribut, und setzen Sie die `IsValid`-Methode außer Kraft. Die `IsValid`-Methode akzeptiert zwei Parameter: Bei dem ersten handelt es sich um ein Objekt mit dem Namen *value* und bei dem zweiten handelt es sich um ein `ValidationContext`-Objekt mit dem Namen *validationContext*. *Value* bezieht sich auf den tatsächlichen Wert aus dem Feld, das Ihr benutzerdefiniertes Validierungssteuerelement überprüft.

Im folgenden Beispiel legt eine Unternehmensregel fest, dass Benutzer das Genre für Filme, die nach 1960 veröffentlicht wurden, möglicherweise nicht auf *Klassiker* festlegen. Das `[ClassicMovie]`-Attribut überprüft erst das Genre, und wenn es sich um einen Klassiker handelt, überprüft es, ob das Datum der Veröffentlichung nach 1960 liegt. Wenn der Film nach 1960 veröffentlicht wurde, schlägt die Validierung fehl. Das Attribut akzeptiert einen Integer-Parameter, der für das Jahr steht, das Sie verwenden können, um Daten zu überprüfen. Sie können wie folgt den Wert des Parameters im Konstruktor des Attributs erfassen:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

Die obenstehende `movie`-Variable stellt ein `Movie`-Objekt dar, das die Daten der Formularübermittlung enthalten, die überprüft werden sollen. In diesem Fall überprüft der Validierungscode anhand der Regeln das Datum und das Genre der `ClassicMovieAttribute`-Klasse in der `IsValid`-Methode. Ist die Validierung erfolgreich, gibt `IsValid` einen `ValidationResult.Success`-Code zurück. Schlägt die Validierung fehl, wird ein `ValidationResult` mit einer Fehlermeldung zurückgegeben:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_GetErrorMessage)]

Wenn ein Benutzer das `Genre`-Feld verändert und das Formular übermittelt, überprüft die `IsValid`-Methode des `ClassicMovieAttribute`-Attributs, ob es sich bei dem Film um einen Klassiker handelt. Wenden Sie das `ClassicMovieAttribute`-Attribut wie jedes andere Attribut auch auf eine Eigenschaft wie `ReleaseDate` an, um wie im folgenden Codebeispiel dargestellt sicherzustellen, dass die Validierung durchgeführt wird. Da das Beispiel nur mit `Movie`-Typen funktioniert, ist es die bessere Option, `IValidatableObject` wie im folgenden Abschnitt dargestellt zu verwenden.

Stattdessen kann derselbe Code auch in dem Modell platziert werden, indem er in die `Validate`-Methode auf der `IValidatableObject`-Schnittstelle implementiert wird. Benutzerdefinierte Validierungsattribute funktionieren zwar gut für die Validierung von individuellen Eigenschaften, die Implementierung von `IValidatableObject` kann jedoch verwendet werden, um die Validierung auf Klassenebene zu implementieren:

[!code-csharp[](validation/sample/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="client-side-validation"></a>Validierung auf Clientseite

Die Validierung auf Clientseite ist ein praktisches Feature für Benutzer. Sie spart Zeit, die andernfalls verloren gehen würde, während sie auf einen Roundtrip auf den Server warten. In Unternehmen reicht es schon aus, wenn nur Bruchteile von Sekunden mehrere Hundert Male täglich verloren gehen, damit viel Zeit und Aufwand unnötig aufgewendet werden und die Frustration wächst. Wenn die Prüfung unkompliziert und ohne Umschweife ausgeführt wird, können Benutzer effizienter arbeiten und Eingaben und Ausgaben von besserer Qualität produzieren.

Damit die Validierung auf Clientseite wie im folgenden Codebeispiel dargestellt funktioniert, müssen Sie über eine Ansicht mit den passenden Skriptverweisen für JavaScript verfügen.

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

Bei dem [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive)-Skript handelt es sich um eine benutzerdefinierte Front-End-Bibliothek von Microsoft, die auf dem beliebten [jQuery Validate](https://jqueryvalidation.org/)-Plugin basiert. Ohne dieses Skript müssen Sie dieselbe Validierungslogik an zwei unterschiedlichen Stellen codieren: einmal in den Attributen der Validierung auf Serverseite für Modelleigenschaften und einmal in den Skripts auf Clientseite. In den Beispielen für die [`validate()`](https://jqueryvalidation.org/validate/)-Methode von „jQuery Validate“ können Sie sehen, wie kompliziert dies werden kann. Stattdessen verwenden die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und die [HTML-Hilfsprogramme](xref:mvc/views/overview) von MVC die Validierungsattribute und Typmetadaten aus den Modelleigenschaften, um HTML 5-[Datenattribute](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) in den Formularelementen zu rendern, die validiert werden müssen. MVC generiert die `data-`-Attribute für integrierte und benutzerdefinierte Attribute. „jQuery Unobtrusive Validation“ analysiert dann diese `data-`-Attribute und übergibt die Logik an „jQuery Validate“, wodurch die Logik der Validierung auf Serverseite in den Client „kopiert“ wird. Sie können Validierungsfehler im Client anzeigen, indem Sie die relevanten Taghilfsprogramme wie folgt verwenden:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Die obenstehenden Taghilfsprogramme rendern die nachfolgende HTML. Beachten Sie, dass die `data-`-Attribute in der HTML-Ausgabe mit den Validierungsattributen für die `ReleaseDate`-Eigenschaft übereinstimmen. Das nachfolgende `data-val-required`-Attribut enthält Fehlermeldungen, um anzuzeigen, ob der Benutzer das Datumsfeld für die Veröffentlichung nicht ausfüllt. „jQuery Unobtrusive Validation“ übergibt diesen Wert an die [`required()`](https://jqueryvalidation.org/required-method/)-Methode von „jQuery Unobtrusive Validation“, die dann diese Meldung im zugehörigen **\<span>**-Element anzeigt.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Die Überprüfung auf Clientseite verhindert die Übermittlung solange, bis das Formular gültig ist. Die Schaltfläche „Übermitteln“ führt JavaScript aus, das entweder das Formular übermittelt oder Fehlermeldungen anzeigt.

MVC legt die Typattributwerte anhand des .NET-Datentyps einer Eigenschaft fest, die möglicherweise durch `[DataType]`-Attribute überschrieben wird. Das Basisattribut `[DataType]` führt keine richtige Validierung auf Serverseite aus. Browser wählen ihre eigenen Fehlermeldungen aus und zeigen diese Fehler nach Belieben an. Das Paket „jQuery Validation Unobtrusive“ kann die Meldungen jedoch überschreiben und sie mit anderen zusammen anzeigen. Dies geschieht am deutlichsten, wenn Benutzer `[DataType]`-Unterklassen wie `[EmailAddress]` anwenden.

### <a name="add-validation-to-dynamic-forms"></a>Hinzufügen der Validierung zu dynamischen Formularen

Da „jQuery Unobtrusive Validation“ die Validierungslogik und -parameter an „jQuery Validate“ übergibt, wenn die Seite das erste Mal geladen wird, führen dynamisch generierte Formulare nicht automatisch eine Validierung durch. Stattdessen müssen Sie „ jQuery Unobtrusive Validation“ auffordern, das dynamische Formular direkt nach dem Erstellen zu analysieren. Beispielweise wird im nachfolgenden Beispiel dargestellt, wie Sie die Validierung auf Clientseite für ein Formular einrichten können, das über AJAX hinzugefügt wurde.

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

Die `$.validator.unobtrusive.parse()`-Methode akzeptiert einen jQuery-Selektor für ihr einziges Argument. Diese Methode fordert „jQuery Unobtrusive Validation“ auf, die `data-`-Attribute von Formularen in diesem Selektor zu analysieren. Die Werte dieser Attribute werden dann an das „jQuery Validate“-Plugin übergeben, sodass das Formular die gewünschten Überprüfungsregeln auf Clientseite ausführt.

### <a name="add-validation-to-dynamic-controls"></a>Hinzufügen der Validierung zu dynamischen Steuerelementen

Sie können jetzt die Validierungsregeln für ein Formular aktualisieren, wenn individuelle Steuerelemente wie `<input/>` und `<select/>` dynamisch generiert werden. Sie können keine Selektoren für die Elemente direkt an die `parse()`-Methode übergeben, da das umgebende Formular bereits analysiert wurde und nicht mehr aktualisiert werden kann. Entfernen Sie stattdessen zuerst die vorhanden Validierungsdaten, und analysieren Sie dann wie nachfolgend dargestellt das ganze Formular erneut:

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="iclientmodelvalidator"></a>IClientModelValidator

Wenn Sie clientseitige Logik für Ihr benutzerdefiniertes Attribut erstellen möchten, führt eine [unaufdringliche Überprüfung](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html), die einen Adapter für die [jQuery-Überprüfung](http://jqueryvalidation.org/documentation/) erstellt, diese automatisch als Teil der Überprüfung auf dem Client aus. Kontrollieren Sie im ersten Schritt, welche Datenattribute hinzugefügt wurden, indem Sie die `IClientModelValidator`-Schnittstelle wie im Folgenden dargestellt implementieren:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_AddValidation)]

Attribute, die diese Schnittstelle implementieren, können HTML-Attribute zu generierten Feldern hinzufügen. Bei der Untersuchung der Ausgabe für das `ReleaseDate`-Element wird HTML angezeigt, die der im vorherigen Beispiel ähnlich ist. Allerdings gibt es jetzt ein `data-val-classicmovie`-Attribut, das in der `AddValidation`-Methode von `IClientModelValidator` definiert wurde.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

Die unaufdringliche Validierung verwendet die Daten in den `data-`-Attributen, die die Fehlermeldungen anzeigen sollen. Allerdings kennt „jQuery“ Regeln oder Meldungen erst, wenn Sie sie zum `validator`-Objekt von „jQuery“ hinzugefügt haben. Dies wird im folgenden Beispiel gezeigt, bei dem eine benutzerdefinierte `classicmovie`-Clientvalidierungsmethode zum jQuery-`validator`-Objekt hinzugefügt wird. Eine Erläuterung der `unobtrusive.adapters.add`-Methode finden Sie in [Unobtrusive Client Validation in ASP.NET-MVC](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html).

[!code-javascript[](validation/sample/Views/Movies/Create.cshtml?name=snippet_UnobtrusiveValidation)]

Mit dem vorhergehenden Code führt die `classicmovie`-Methode eine clientseitige Validierung am Veröffentlichungsdatum des Films durch. Die Fehlermeldung wird angezeigt, wenn die Methode `false` zurückgibt.

## <a name="remote-validation"></a>Remotevalidierung

Die Remotevalidierung ist ein nützliches Feature, das Sie verwenden können, wenn Sie Daten auf dem Client mit Daten auf dem Server abgleichen müssen. Beispielsweise muss Ihre App möglicherweise überprüfen, ob eine E-Mail oder ein Benutzername bereits verwendet wird. Dafür muss sie eine große Menge an Daten abfragen. Wenn große Mengen an Daten heruntergeladen werden, um mindestens ein Feld zu überprüfen, werden zu viele Ressourcen verbraucht. Außerdem kann es sein, dass vertrauliche Informationen verfügbar gemacht werden. Stattdessen können Sie auch eine Roundtripanforderung senden, um ein Feld zu überprüfen.

Sie können die Remotevalidierung in zwei Schritten implementieren. Im ersten Schritt müssen Sie den Änderungsverlauf Ihres Modells mit dem `[Remote]`-Attribut einblenden. Das `[Remote]`-Attribut akzeptiert mehrere Überladungen, die Sie verwenden können, um clientseitiges JavaScript an den Code weiterzuleiten, der aufgerufen werden soll. Das nachfolgende Beispiel zeigt auf die `VerifyEmail`-Aktionsmethode des `Users`-Controllers.

[!code-csharp[](validation/sample/User.cs?name=snippet_UserEmailProperty)]

Im zweiten Schritt müssen Sie den Validierungscode in die zugehörige Aktionsmethode übergeben, die im `[Remote]`-Attribut definiert ist. Gemäß der jQuery Validate-[remote](https://jqueryvalidation.org/remote-method/)-Methodendokumentation muss die Serverantwort eine JSON-Zeichenfolge sein, die entweder:

* `"true"` ist für gültige Elemente.
* `"false"`, `undefined` oder `null` ist für ungültige Elemente. Es wird die Standardfehlermeldung angezeigt.

Wenn die Serverantwort eine Zeichenfolge ist (z.B. `"That name is already taken, try peter123 instead"`), wird die Zeichenfolge als benutzerdefinierte Fehlermeldung anstelle der Standardzeichenfolge angezeigt.

Die Definition der `VerifyEmail`-Methode hält sich wie nachfolgend dargestellt an diese Regeln. Sie gibt eine Fehlermeldung für die Validierung zurück, wenn die E-Mail-Adresse bereits vergeben ist, oder `true`, wenn die E-Mail-Adresse frei ist, und umschließt das Ergebnis in einem `JsonResult`-Objekt. Die Clientseite kann dann den Rückgabewert verwenden, um fortzufahren oder den Fehler ggf. anzuzeigen.

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyEmail)]

Wenn Benutzer jetzt eine E-Mail-Adresse eingeben, führt JavaScript in der Ansicht einen Remoteaufruf durch, um zu überprüfen, ob die E-Mail-Adresse bereits vergeben ist, und zeigt einen Fehler an, falls dies der Fall ist. Andernfalls kann der Benutzer das Formular wie gewöhnlich übermitteln.

Die `AdditionalFields`-Eigenschaft des `[Remote]`-Attributs ist nützlich für die Validierung von Kombinationen von Feldern im Vergleich zu Daten auf dem Server. Wenn z.B. das oben genannte `User`-Modell über zwei zusätzliche Eigenschaften verfügt, die `FirstName` und `LastName` aufrufen, sollten Sie überprüfen, dass kein bereits vorhandener Benutzer diese Namenskombination verwendet. Sie können die neuen Eigenschaften wie im folgenden Codebeispiel dargestellt definieren:

[!code-csharp[](validation/sample/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` kann explizit auf die Zeichenfolgen `"FirstName"` und `"LastName"` festgelegt werden. Wenn Sie aber so den [`nameof`](/dotnet/csharp/language-reference/keywords/nameof)-Operator verwenden, ist ein Refactoring zu einem späteren Zeitpunkt einfacher. Die Aktionsmethode muss dann zwei Argumente akzeptieren, um die Validierung auszuführen: eins für den Wert von `FirstName` und eins für den Wert von `LastName`.

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyName)]

Wenn Benutzer jetzt einen Vor- und einen Nachnamen eingeben, führt JavaScript folgende Vorgänge aus:

* Es führt einen Remoteaufruf durch, um zu überprüfen, ob diese Namenskombination bereits verwendet wurde.
* Wenn dem so ist, wird eine Fehlermeldung angezeigt.
* Andernfalls kann der Benutzer das Formular übermitteln.

Wenn Sie mindestens zwei weitere Felder mit dem `[Remote]`-Attribut überprüfen müssen, geben Sie sie als eine mit Kommas getrennte Liste an. Wenn Sie z.B. dem Modell eine `MiddleName`-Eigenschaft hinzufügen möchten, legen Sie das `[Remote]`-Attribut wie im folgenden Code veranschaulicht fest:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields` muss wie alle anderen Attributargumente ein konstanter Ausdruck sein. Aus diesem Grund müssen Sie keine [interpolierte Zeichenfolge verwenden](/dotnet/csharp/language-reference/keywords/interpolated-strings) oder <xref:System.String.Join*> aufrufen, um `AdditionalFields` zu initialisieren. Für jedes weitere Feld, das Sie dem `[Remote]`-Attribut hinzufügen, müssen Sie der zugehörigen Aktionsmethode des Controllers ein weiteres Argument hinzufügen.
