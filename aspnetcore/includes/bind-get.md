> [!WARNING]
> Aus Sicherheitsgründen müssen Sie Daten von `GET`-Anforderungen in die Seitenmodelleigenschaften einbinden. Überprüfen Sie die Benutzereingaben, bevor Sie sie den Eigenschaften zuordnen. Die Verwendung der `GET`-Bindung ist von Vorteil, wenn Sie Szenarios behandeln, die von Abfragezeichenfolgen oder Routenwerten abhängig sind.
>
> Legen Sie die `SupportsGet`-Eigenschaft des [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attributs auf `true` fest, um eine Eigenschaft an `GET`-Anforderungen zu binden:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Weitere Informationen finden Sie im Video [ASP.NET Core Community Standup: Bind on GET discussion (YouTube) (ASP.NET Core Community Standup: Diskussion zur Bindung an GET)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
