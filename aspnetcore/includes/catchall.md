> [!WARNING]
> <span data-ttu-id="36de5-101">Ein **catch-all**-Parameter kann aufgrund eines [Fehlers](https://github.com/dotnet/aspnetcore/issues/18677) beim Routing nicht ordnungsgemäß mit Routen übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="36de5-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="36de5-102">Apps, die von diesem Fehler betroffen sind, weisen die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="36de5-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="36de5-103">Eine catch-all-Route, zum Beispiel `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="36de5-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="36de5-104">Die catch-all-Route kann nicht mit Anforderungen abgeglichen werden, die abgeglichen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="36de5-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="36de5-105">Durch das Entfernen anderer Routen funktioniert die catch-all-Route.</span><span class="sxs-lookup"><span data-stu-id="36de5-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="36de5-106">Weitere Beispiele zu diesem Fehler finden Sie in den GitHub-Issues [18677](https://github.com/dotnet/aspnetcore/issues/18677) und [16579](https://github.com/dotnet/aspnetcore/issues/16579).</span><span class="sxs-lookup"><span data-stu-id="36de5-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="36de5-107">Für diesen Fehler ist eine Opt-in-Behebung geplant.</span><span class="sxs-lookup"><span data-stu-id="36de5-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="36de5-108">Dieses Dokument wird aktualisiert, wenn der Patch veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="36de5-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="36de5-109">Wenn der Patch veröffentlicht wird, legt der folgende Code einen internen Switch fest, mit dem dieser Fehle behoben wird:</span><span class="sxs-lookup"><span data-stu-id="36de5-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```