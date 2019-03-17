---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665750"
---
# <a name="error-handling-sample-application"></a><span data-ttu-id="a65dd-101">Beispielanwendung zur Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="a65dd-101">Error Handling Sample Application</span></span>

<span data-ttu-id="a65dd-102">Diese Beispiel-App veranschaulicht die im Thema [Fehlerbehandlung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) beschriebenen Szenarios.</span><span class="sxs-lookup"><span data-stu-id="a65dd-102">This sample app demonstrates the scenarios described in the [Handle errors in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) topic.</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="a65dd-103">Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="a65dd-103">Configure a custom exception handling page</span></span>

<span data-ttu-id="a65dd-104">Wenn die App nicht in der Entwicklungsumgebung ausgeführt wird, führt die Middleware zur Ausnahmebehandlung Folgendes durch:</span><span class="sxs-lookup"><span data-stu-id="a65dd-104">When the app isn't running in the Development environment, Exception Handling Middleware:</span></span>

* <span data-ttu-id="a65dd-105">Fängt Ausnahmen ab.</span><span class="sxs-lookup"><span data-stu-id="a65dd-105">Catches exceptions.</span></span>
* <span data-ttu-id="a65dd-106">Protokolliert Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="a65dd-106">Logs exceptions.</span></span>
* <span data-ttu-id="a65dd-107">Führt erneut die Anforderung in einer anderen Pipeline im angegebenen Pfad aus.</span><span class="sxs-lookup"><span data-stu-id="a65dd-107">Re-executes the request in an alternate pipeline at the path provided.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="a65dd-108">Konfigurieren eines benutzerdefinierten Ausnahmeverarbeitungscodes</span><span class="sxs-lookup"><span data-stu-id="a65dd-108">Configure custom exception handling code</span></span>

<span data-ttu-id="a65dd-109">Eine Alternative zur Bereitstellung eines Endpunkts für Fehler bei einer benutzerdefinierten Ausnahmebehandlungsseite besteht darin, einen Lambda-Ausdruck für `UseExceptionHandler` anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a65dd-109">An alternative to serving an endpoint for errors with a custom exception handling page is to provide a lambda to `UseExceptionHandler`.</span></span> <span data-ttu-id="a65dd-110">Die Verwendung eines Lambda-Ausdrucks mit `UseExceptionHandler` ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a65dd-110">Using a lambda with `UseExceptionHandler` allows access to the error before returning the response.</span></span>

<span data-ttu-id="a65dd-111">Die Beispiel-App veranschaulicht benutzerdefinierten Ausnahmebehandlungscode in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a65dd-111">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="a65dd-112">Befolgen Sie die Anweisungen am Anfang der Datei *Startup.cs* (`LambdaErrorHandler`).</span><span class="sxs-lookup"><span data-stu-id="a65dd-112">Follow the instructions at the top of the *Startup.cs* file (`LambdaErrorHandler`).</span></span> <span data-ttu-id="a65dd-113">Lösen Sie nach Start der App eine Ausnahme mit dem Link **Ausnahme auslösen** auf der Indexseite aus.</span><span class="sxs-lookup"><span data-stu-id="a65dd-113">After the app starts, trigger an exception with the **Throw Exception** link on the Index page.</span></span>
