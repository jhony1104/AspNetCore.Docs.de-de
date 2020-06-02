---
Title: einfache Autorisierung in ASP.net Core Author: Rick-Anderson Description: erfahren Sie, wie Sie das Attribut "autorisieren" verwenden, um den Zugriff auf ASP.net Core Controller und Aktionen einzuschränken.
ms. Author: Riande ms. Date: 10/14/2016 NO-LOC:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: Security/Authorization/Simple

---
# <a name="simple-authorization-in-aspnet-core"></a>Einfache Autorisierung in ASP.net Core

<a name="security-authorization-simple"></a>

Die Autorisierung in ASP.net Core wird mit <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> und den verschiedenen Parametern gesteuert. In seiner einfachsten Form schränkt das Anwenden des- `[Authorize]` Attributs auf einen Controller, eine Aktion oder eine Razor Seite den Zugriff auf diese Komponente auf einen beliebigen authentifizierten Benutzer ein.

Der folgende Code schränkt z. b. den Zugriff auf `AccountController` alle authentifizierten Benutzer ein.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das- `AuthorizeAttribute` Attribut auf die eigentliche Aktion an:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.

Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen. Zum Beispiel:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Dies erlaubt nur authentifizierte Benutzer, `AccountController` außer bei der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.

> [!WARNING]
> `[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen. Wenn Sie `[AllowAnonymous]` und ein beliebiges Attribut kombinieren `[Authorize]` , `[Authorize]` werden die Attribute ignoriert. Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.
