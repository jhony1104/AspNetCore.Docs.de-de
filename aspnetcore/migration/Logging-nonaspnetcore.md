---
<span data-ttu-id="7ae79-101">Title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0 Author: pakrym Description: erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.</span><span class="sxs-lookup"><span data-stu-id="7ae79-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="7ae79-102">ms. Author: pakrym ms. Custom: MVC ms. Date: 01/04/2019 NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="7ae79-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="7ae79-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7ae79-103">'Blazor'</span></span>
- <span data-ttu-id="7ae79-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7ae79-104">'Identity'</span></span>
- <span data-ttu-id="7ae79-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7ae79-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="7ae79-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7ae79-106">'Razor'</span></span>
- <span data-ttu-id="7ae79-107">' SignalR ' UID: Migration/Protokollierung-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="7ae79-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="7ae79-108">Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0</span><span class="sxs-lookup"><span data-stu-id="7ae79-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="7ae79-109">In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` zwischen 2,1 und 2,2 oder 3,0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="7ae79-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="7ae79-110">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="7ae79-110">2.1 to 2.2</span></span>

<span data-ttu-id="7ae79-111">Erstellen Sie manuell `ServiceCollection` und rufen Sie auf `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="7ae79-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="7ae79-112">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ae79-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="7ae79-113">2,2 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ae79-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="7ae79-114">2,1 bis 3,0</span><span class="sxs-lookup"><span data-stu-id="7ae79-114">2.1 to 3.0</span></span>

<span data-ttu-id="7ae79-115">Verwenden Sie in 3,0 `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="7ae79-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="7ae79-116">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ae79-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="7ae79-117">3,0 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ae79-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="7ae79-118">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7ae79-118">Additional resources</span></span>

* <span data-ttu-id="7ae79-119">Das [nuget-Paket Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="7ae79-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
