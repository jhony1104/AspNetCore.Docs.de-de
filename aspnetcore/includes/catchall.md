> [!WARNING]
> Ein **catch-all**-Parameter kann aufgrund eines [Fehlers](https://github.com/dotnet/aspnetcore/issues/18677) beim Routing nicht ordnungsgemäß mit Routen übereinstimmen. Apps, die von diesem Fehler betroffen sind, weisen die folgenden Merkmale auf:
>
> * Eine catch-all-Route, zum Beispiel `{**slug}"`
> * Die catch-all-Route kann nicht mit Anforderungen abgeglichen werden, die abgeglichen werden sollen.
> * Durch das Entfernen anderer Routen funktioniert die catch-all-Route.
>
> Weitere Beispiele zu diesem Fehler finden Sie in den GitHub-Issues [18677](https://github.com/dotnet/aspnetcore/issues/18677) und [16579](https://github.com/dotnet/aspnetcore/issues/16579).
>
> Eine Opt-in-Lösung für diesen Fehler ist im [.net Core 3.1.301 SDK und](https://dotnet.microsoft.com/download/dotnet-core/3.1)höher enthalten. Der folgende Code legt einen internen Switch fest, mit dem dieser Fehler behoben wird:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```