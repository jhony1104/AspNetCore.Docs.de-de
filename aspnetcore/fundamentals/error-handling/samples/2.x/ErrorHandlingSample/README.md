---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665750"
---
# <a name="error-handling-sample-application"></a>Beispielanwendung zur Fehlerbehandlung

Diese Beispiel-App veranschaulicht die im Thema [Fehlerbehandlung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) beschriebenen Szenarios.

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung

Wenn die App nicht in der Entwicklungsumgebung ausgeführt wird, führt die Middleware zur Ausnahmebehandlung Folgendes durch:

* Fängt Ausnahmen ab.
* Protokolliert Ausnahmen.
* Führt erneut die Anforderung in einer anderen Pipeline im angegebenen Pfad aus.

## <a name="configure-custom-exception-handling-code"></a>Konfigurieren eines benutzerdefinierten Ausnahmeverarbeitungscodes

Eine Alternative zur Bereitstellung eines Endpunkts für Fehler bei einer benutzerdefinierten Ausnahmebehandlungsseite besteht darin, einen Lambda-Ausdruck für `UseExceptionHandler` anzugeben. Die Verwendung eines Lambda-Ausdrucks mit `UseExceptionHandler` ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.

Die Beispiel-App veranschaulicht benutzerdefinierten Ausnahmebehandlungscode in `Startup.Configure`. Befolgen Sie die Anweisungen am Anfang der Datei *Startup.cs* (`LambdaErrorHandler`). Lösen Sie nach Start der App eine Ausnahme mit dem Link **Ausnahme auslösen** auf der Indexseite aus.
