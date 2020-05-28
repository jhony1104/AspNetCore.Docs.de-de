---
<span data-ttu-id="f380c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-102">'Blazor'</span></span>
- <span data-ttu-id="f380c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-103">'Identity'</span></span>
- <span data-ttu-id="f380c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-105">'Razor'</span></span>
- <span data-ttu-id="f380c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f380c-107">Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f380c-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f380c-108">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="f380c-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f380c-109">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="f380c-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f380c-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f380c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f380c-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f380c-111">Prerequisites</span></span>

<span data-ttu-id="f380c-112">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f380c-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f380c-113">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="f380c-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f380c-114">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f380c-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f380c-115">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f380c-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f380c-116">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="f380c-116">Set the client ID and client secret</span></span>

<span data-ttu-id="f380c-117">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="f380c-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f380c-118">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="f380c-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f380c-119">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f380c-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f380c-120">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="f380c-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f380c-121">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f380c-122">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f380c-123">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f380c-124">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f380c-125">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="f380c-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f380c-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f380c-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f380c-127">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="f380c-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f380c-128">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="f380c-128">Establish the authentication scope</span></span>

<span data-ttu-id="f380c-129">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie angeben <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f380c-130">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f380c-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f380c-131">Anbieter</span><span class="sxs-lookup"><span data-stu-id="f380c-131">Provider</span></span>  | <span data-ttu-id="f380c-132">`Scope`</span><span class="sxs-lookup"><span data-stu-id="f380c-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f380c-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-134">'Blazor'</span></span>
- <span data-ttu-id="f380c-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-135">'Identity'</span></span>
- <span data-ttu-id="f380c-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-137">'Razor'</span></span>
- <span data-ttu-id="f380c-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-140">'Blazor'</span></span>
- <span data-ttu-id="f380c-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-141">'Identity'</span></span>
- <span data-ttu-id="f380c-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-143">'Razor'</span></span>
- <span data-ttu-id="f380c-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-144">'SignalR' uid:</span></span> 

<span data-ttu-id="f380c-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-146">'Blazor'</span></span>
- <span data-ttu-id="f380c-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-147">'Identity'</span></span>
- <span data-ttu-id="f380c-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-149">'Razor'</span></span>
- <span data-ttu-id="f380c-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-152">'Blazor'</span></span>
- <span data-ttu-id="f380c-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-153">'Identity'</span></span>
- <span data-ttu-id="f380c-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-155">'Razor'</span></span>
- <span data-ttu-id="f380c-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-158">'Blazor'</span></span>
- <span data-ttu-id="f380c-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-159">'Identity'</span></span>
- <span data-ttu-id="f380c-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-161">'Razor'</span></span>
- <span data-ttu-id="f380c-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-164">'Blazor'</span></span>
- <span data-ttu-id="f380c-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-165">'Identity'</span></span>
- <span data-ttu-id="f380c-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-167">'Razor'</span></span>
- <span data-ttu-id="f380c-168">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-170">'Blazor'</span></span>
- <span data-ttu-id="f380c-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-171">'Identity'</span></span>
- <span data-ttu-id="f380c-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-173">'Razor'</span></span>
- <span data-ttu-id="f380c-174">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-176">'Blazor'</span></span>
- <span data-ttu-id="f380c-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-177">'Identity'</span></span>
- <span data-ttu-id="f380c-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-179">'Razor'</span></span>
- <span data-ttu-id="f380c-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-182">'Blazor'</span></span>
- <span data-ttu-id="f380c-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-183">'Identity'</span></span>
- <span data-ttu-id="f380c-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-185">'Razor'</span></span>
- <span data-ttu-id="f380c-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-188">'Blazor'</span></span>
- <span data-ttu-id="f380c-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-189">'Identity'</span></span>
- <span data-ttu-id="f380c-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-191">'Razor'</span></span>
- <span data-ttu-id="f380c-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-194">'Blazor'</span></span>
- <span data-ttu-id="f380c-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-195">'Identity'</span></span>
- <span data-ttu-id="f380c-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-197">'Razor'</span></span>
- <span data-ttu-id="f380c-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-200">'Blazor'</span></span>
- <span data-ttu-id="f380c-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-201">'Identity'</span></span>
- <span data-ttu-id="f380c-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-203">'Razor'</span></span>
- <span data-ttu-id="f380c-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-206">'Blazor'</span></span>
- <span data-ttu-id="f380c-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-207">'Identity'</span></span>
- <span data-ttu-id="f380c-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-209">'Razor'</span></span>
- <span data-ttu-id="f380c-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-212">'Blazor'</span></span>
- <span data-ttu-id="f380c-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-213">'Identity'</span></span>
- <span data-ttu-id="f380c-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-215">'Razor'</span></span>
- <span data-ttu-id="f380c-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-218">'Blazor'</span></span>
- <span data-ttu-id="f380c-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-219">'Identity'</span></span>
- <span data-ttu-id="f380c-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-221">'Razor'</span></span>
- <span data-ttu-id="f380c-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-224">'Blazor'</span></span>
- <span data-ttu-id="f380c-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-225">'Identity'</span></span>
- <span data-ttu-id="f380c-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-227">'Razor'</span></span>
- <span data-ttu-id="f380c-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-230">'Blazor'</span></span>
- <span data-ttu-id="f380c-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-231">'Identity'</span></span>
- <span data-ttu-id="f380c-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-233">'Razor'</span></span>
- <span data-ttu-id="f380c-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-236">'Blazor'</span></span>
- <span data-ttu-id="f380c-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-237">'Identity'</span></span>
- <span data-ttu-id="f380c-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-239">'Razor'</span></span>
- <span data-ttu-id="f380c-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-242">'Blazor'</span></span>
- <span data-ttu-id="f380c-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-243">'Identity'</span></span>
- <span data-ttu-id="f380c-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-245">'Razor'</span></span>
- <span data-ttu-id="f380c-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-248">'Blazor'</span></span>
- <span data-ttu-id="f380c-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-249">'Identity'</span></span>
- <span data-ttu-id="f380c-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-251">'Razor'</span></span>
- <span data-ttu-id="f380c-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-254">'Blazor'</span></span>
- <span data-ttu-id="f380c-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-255">'Identity'</span></span>
- <span data-ttu-id="f380c-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-257">'Razor'</span></span>
- <span data-ttu-id="f380c-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-260">'Blazor'</span></span>
- <span data-ttu-id="f380c-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-261">'Identity'</span></span>
- <span data-ttu-id="f380c-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-263">'Razor'</span></span>
- <span data-ttu-id="f380c-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-266">'Blazor'</span></span>
- <span data-ttu-id="f380c-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-267">'Identity'</span></span>
- <span data-ttu-id="f380c-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-269">'Razor'</span></span>
- <span data-ttu-id="f380c-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-272">'Blazor'</span></span>
- <span data-ttu-id="f380c-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-273">'Identity'</span></span>
- <span data-ttu-id="f380c-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-275">'Razor'</span></span>
- <span data-ttu-id="f380c-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-278">'Blazor'</span></span>
- <span data-ttu-id="f380c-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-279">'Identity'</span></span>
- <span data-ttu-id="f380c-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-281">'Razor'</span></span>
- <span data-ttu-id="f380c-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-284">'Blazor'</span></span>
- <span data-ttu-id="f380c-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-285">'Identity'</span></span>
- <span data-ttu-id="f380c-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-287">'Razor'</span></span>
- <span data-ttu-id="f380c-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-290">'Blazor'</span></span>
- <span data-ttu-id="f380c-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-291">'Identity'</span></span>
- <span data-ttu-id="f380c-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-293">'Razor'</span></span>
- <span data-ttu-id="f380c-294">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-296">'Blazor'</span></span>
- <span data-ttu-id="f380c-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-297">'Identity'</span></span>
- <span data-ttu-id="f380c-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-299">'Razor'</span></span>
- <span data-ttu-id="f380c-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-302">'Blazor'</span></span>
- <span data-ttu-id="f380c-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-303">'Identity'</span></span>
- <span data-ttu-id="f380c-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-305">'Razor'</span></span>
- <span data-ttu-id="f380c-306">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-308">'Blazor'</span></span>
- <span data-ttu-id="f380c-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-309">'Identity'</span></span>
- <span data-ttu-id="f380c-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-311">'Razor'</span></span>
- <span data-ttu-id="f380c-312">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-314">'Blazor'</span></span>
- <span data-ttu-id="f380c-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-315">'Identity'</span></span>
- <span data-ttu-id="f380c-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-317">'Razor'</span></span>
- <span data-ttu-id="f380c-318">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-320">'Blazor'</span></span>
- <span data-ttu-id="f380c-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-321">'Identity'</span></span>
- <span data-ttu-id="f380c-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-323">'Razor'</span></span>
- <span data-ttu-id="f380c-324">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-324">'SignalR' uid:</span></span> 

<span data-ttu-id="f380c-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f380c-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f380c-326">In der Beispiel-APP `userinfo.profile` wird der Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> für aufgerufen wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f380c-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f380c-327">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="f380c-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f380c-328">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="f380c-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f380c-329">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-329">Map user data keys and create claims</span></span>

<span data-ttu-id="f380c-330">Geben Sie in den Optionen des Anbieters <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> in den JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="f380c-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f380c-331">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f380c-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f380c-332">Die Beispiel-App erstellt Gebiets Schema-( `urn:google:locale` ) und Bild ( `urn:google:picture` )-Ansprüche aus den `locale` `picture` Schlüsseln und in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="f380c-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f380c-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) bei der APP mit signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f380c-334">Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.Identity.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f380c-335">In der Beispiel-APP `OnPostConfirmationAsync` richtet (*Account/externzuweisung. cshtml. cs*) die locale ( `urn:google:locale` )-und Image ( `urn:google:picture` )-Ansprüche für die angemeldete `ApplicationUser` ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f380c-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f380c-336">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f380c-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f380c-337">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="f380c-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f380c-338">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="f380c-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f380c-339">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="f380c-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="f380c-340">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="f380c-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f380c-341">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="f380c-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f380c-342">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die cookeauthentifizierungs-Middleware <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> , um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="f380c-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f380c-343">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="f380c-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f380c-344">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="f380c-344">Save the access token</span></span>

<span data-ttu-id="f380c-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definiert, ob Zugriffs-und Aktualisierungs Token <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung in der gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f380c-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f380c-346">`SaveTokens`wird standardmäßig auf festgelegt `false` , um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="f380c-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f380c-347">Die Beispiel-App legt den Wert von `SaveTokens` auf `true` in fest <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f380c-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f380c-348">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser` der `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f380c-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f380c-349">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f380c-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f380c-350">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="f380c-350">How to add additional custom tokens</span></span>

<span data-ttu-id="f380c-351">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von gespeichert wird `SaveTokens` , fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f380c-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f380c-352">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-352">Creating and adding claims</span></span>

<span data-ttu-id="f380c-353">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f380c-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f380c-354">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="f380c-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f380c-355">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von ableiten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und die abstrakte- <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="f380c-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f380c-356">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f380c-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f380c-357">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="f380c-358">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f380c-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f380c-359">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="f380c-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f380c-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="f380c-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f380c-361">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="f380c-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f380c-362">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="f380c-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f380c-363">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="f380c-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f380c-364">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f380c-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f380c-365">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f380c-365">Prerequisites</span></span>

<span data-ttu-id="f380c-366">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f380c-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f380c-367">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="f380c-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f380c-368">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f380c-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f380c-369">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="f380c-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f380c-370">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="f380c-370">Set the client ID and client secret</span></span>

<span data-ttu-id="f380c-371">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="f380c-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f380c-372">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="f380c-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f380c-373">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f380c-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f380c-374">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="f380c-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f380c-375">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f380c-376">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f380c-377">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f380c-378">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f380c-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f380c-379">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="f380c-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f380c-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="f380c-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f380c-381">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="f380c-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f380c-382">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="f380c-382">Establish the authentication scope</span></span>

<span data-ttu-id="f380c-383">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie angeben <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f380c-384">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f380c-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f380c-385">Anbieter</span><span class="sxs-lookup"><span data-stu-id="f380c-385">Provider</span></span>  | <span data-ttu-id="f380c-386">`Scope`</span><span class="sxs-lookup"><span data-stu-id="f380c-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="f380c-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-388">'Blazor'</span></span>
- <span data-ttu-id="f380c-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-389">'Identity'</span></span>
- <span data-ttu-id="f380c-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-391">'Razor'</span></span>
- <span data-ttu-id="f380c-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-394">'Blazor'</span></span>
- <span data-ttu-id="f380c-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-395">'Identity'</span></span>
- <span data-ttu-id="f380c-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-397">'Razor'</span></span>
- <span data-ttu-id="f380c-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-398">'SignalR' uid:</span></span> 

<span data-ttu-id="f380c-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-400">'Blazor'</span></span>
- <span data-ttu-id="f380c-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-401">'Identity'</span></span>
- <span data-ttu-id="f380c-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-403">'Razor'</span></span>
- <span data-ttu-id="f380c-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-406">'Blazor'</span></span>
- <span data-ttu-id="f380c-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-407">'Identity'</span></span>
- <span data-ttu-id="f380c-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-409">'Razor'</span></span>
- <span data-ttu-id="f380c-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-412">'Blazor'</span></span>
- <span data-ttu-id="f380c-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-413">'Identity'</span></span>
- <span data-ttu-id="f380c-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-415">'Razor'</span></span>
- <span data-ttu-id="f380c-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-418">'Blazor'</span></span>
- <span data-ttu-id="f380c-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-419">'Identity'</span></span>
- <span data-ttu-id="f380c-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-421">'Razor'</span></span>
- <span data-ttu-id="f380c-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-424">'Blazor'</span></span>
- <span data-ttu-id="f380c-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-425">'Identity'</span></span>
- <span data-ttu-id="f380c-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-427">'Razor'</span></span>
- <span data-ttu-id="f380c-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-430">'Blazor'</span></span>
- <span data-ttu-id="f380c-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-431">'Identity'</span></span>
- <span data-ttu-id="f380c-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-433">'Razor'</span></span>
- <span data-ttu-id="f380c-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-436">'Blazor'</span></span>
- <span data-ttu-id="f380c-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-437">'Identity'</span></span>
- <span data-ttu-id="f380c-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-439">'Razor'</span></span>
- <span data-ttu-id="f380c-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-442">'Blazor'</span></span>
- <span data-ttu-id="f380c-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-443">'Identity'</span></span>
- <span data-ttu-id="f380c-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-445">'Razor'</span></span>
- <span data-ttu-id="f380c-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-448">'Blazor'</span></span>
- <span data-ttu-id="f380c-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-449">'Identity'</span></span>
- <span data-ttu-id="f380c-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-451">'Razor'</span></span>
- <span data-ttu-id="f380c-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-454">'Blazor'</span></span>
- <span data-ttu-id="f380c-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-455">'Identity'</span></span>
- <span data-ttu-id="f380c-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-457">'Razor'</span></span>
- <span data-ttu-id="f380c-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-460">'Blazor'</span></span>
- <span data-ttu-id="f380c-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-461">'Identity'</span></span>
- <span data-ttu-id="f380c-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-463">'Razor'</span></span>
- <span data-ttu-id="f380c-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-466">'Blazor'</span></span>
- <span data-ttu-id="f380c-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-467">'Identity'</span></span>
- <span data-ttu-id="f380c-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-469">'Razor'</span></span>
- <span data-ttu-id="f380c-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-472">'Blazor'</span></span>
- <span data-ttu-id="f380c-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-473">'Identity'</span></span>
- <span data-ttu-id="f380c-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-475">'Razor'</span></span>
- <span data-ttu-id="f380c-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-478">'Blazor'</span></span>
- <span data-ttu-id="f380c-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-479">'Identity'</span></span>
- <span data-ttu-id="f380c-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-481">'Razor'</span></span>
- <span data-ttu-id="f380c-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-484">'Blazor'</span></span>
- <span data-ttu-id="f380c-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-485">'Identity'</span></span>
- <span data-ttu-id="f380c-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-487">'Razor'</span></span>
- <span data-ttu-id="f380c-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-490">'Blazor'</span></span>
- <span data-ttu-id="f380c-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-491">'Identity'</span></span>
- <span data-ttu-id="f380c-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-493">'Razor'</span></span>
- <span data-ttu-id="f380c-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-496">'Blazor'</span></span>
- <span data-ttu-id="f380c-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-497">'Identity'</span></span>
- <span data-ttu-id="f380c-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-499">'Razor'</span></span>
- <span data-ttu-id="f380c-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-502">'Blazor'</span></span>
- <span data-ttu-id="f380c-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-503">'Identity'</span></span>
- <span data-ttu-id="f380c-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-505">'Razor'</span></span>
- <span data-ttu-id="f380c-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-508">'Blazor'</span></span>
- <span data-ttu-id="f380c-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-509">'Identity'</span></span>
- <span data-ttu-id="f380c-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-511">'Razor'</span></span>
- <span data-ttu-id="f380c-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-514">'Blazor'</span></span>
- <span data-ttu-id="f380c-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-515">'Identity'</span></span>
- <span data-ttu-id="f380c-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-517">'Razor'</span></span>
- <span data-ttu-id="f380c-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-520">'Blazor'</span></span>
- <span data-ttu-id="f380c-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-521">'Identity'</span></span>
- <span data-ttu-id="f380c-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-523">'Razor'</span></span>
- <span data-ttu-id="f380c-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-526">'Blazor'</span></span>
- <span data-ttu-id="f380c-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-527">'Identity'</span></span>
- <span data-ttu-id="f380c-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-529">'Razor'</span></span>
- <span data-ttu-id="f380c-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-532">'Blazor'</span></span>
- <span data-ttu-id="f380c-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-533">'Identity'</span></span>
- <span data-ttu-id="f380c-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-535">'Razor'</span></span>
- <span data-ttu-id="f380c-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-538">'Blazor'</span></span>
- <span data-ttu-id="f380c-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-539">'Identity'</span></span>
- <span data-ttu-id="f380c-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-541">'Razor'</span></span>
- <span data-ttu-id="f380c-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-544">'Blazor'</span></span>
- <span data-ttu-id="f380c-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-545">'Identity'</span></span>
- <span data-ttu-id="f380c-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-547">'Razor'</span></span>
- <span data-ttu-id="f380c-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-550">'Blazor'</span></span>
- <span data-ttu-id="f380c-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-551">'Identity'</span></span>
- <span data-ttu-id="f380c-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-553">'Razor'</span></span>
- <span data-ttu-id="f380c-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-556">'Blazor'</span></span>
- <span data-ttu-id="f380c-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-557">'Identity'</span></span>
- <span data-ttu-id="f380c-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-559">'Razor'</span></span>
- <span data-ttu-id="f380c-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-562">'Blazor'</span></span>
- <span data-ttu-id="f380c-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-563">'Identity'</span></span>
- <span data-ttu-id="f380c-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-565">'Razor'</span></span>
- <span data-ttu-id="f380c-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-568">'Blazor'</span></span>
- <span data-ttu-id="f380c-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-569">'Identity'</span></span>
- <span data-ttu-id="f380c-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-571">'Razor'</span></span>
- <span data-ttu-id="f380c-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f380c-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f380c-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f380c-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f380c-574">'Blazor'</span></span>
- <span data-ttu-id="f380c-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f380c-575">'Identity'</span></span>
- <span data-ttu-id="f380c-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f380c-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="f380c-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f380c-577">'Razor'</span></span>
- <span data-ttu-id="f380c-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f380c-578">'SignalR' uid:</span></span> 

<span data-ttu-id="f380c-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="f380c-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="f380c-580">In der Beispiel-APP `userinfo.profile` wird der Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> für aufgerufen wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f380c-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f380c-581">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="f380c-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f380c-582">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="f380c-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f380c-583">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-583">Map user data keys and create claims</span></span>

<span data-ttu-id="f380c-584">Geben Sie in den Optionen des Anbieters <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> in den JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="f380c-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f380c-585">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="f380c-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f380c-586">Die Beispiel-App erstellt Gebiets Schema-( `urn:google:locale` ) und Bild ( `urn:google:picture` )-Ansprüche aus den `locale` `picture` Schlüsseln und in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="f380c-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f380c-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` wird ein <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) bei der APP mit signiert <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f380c-588">Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.Identity.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="f380c-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f380c-589">In der Beispiel-APP `OnPostConfirmationAsync` richtet (*Account/externzuweisung. cshtml. cs*) die locale ( `urn:google:locale` )-und Image ( `urn:google:picture` )-Ansprüche für die angemeldete `ApplicationUser` ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="f380c-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f380c-590">Standardmäßig werden die Ansprüche eines Benutzers im Authentifizierungs Cookie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f380c-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f380c-591">Wenn das Authentifizierungs Cookie zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="f380c-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f380c-592">Der Browser erkennt, dass der Cookie-Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="f380c-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f380c-593">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="f380c-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="f380c-594">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="f380c-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f380c-595">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="f380c-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f380c-596">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> für die cookeauthentifizierungs-Middleware <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> , um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="f380c-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f380c-597">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="f380c-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f380c-598">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="f380c-598">Save the access token</span></span>

<span data-ttu-id="f380c-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definiert, ob Zugriffs-und Aktualisierungs Token <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung in der gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f380c-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f380c-600">`SaveTokens`wird standardmäßig auf festgelegt `false` , um die Größe des endgültigen Authentifizierungs Cookies zu verringern.</span><span class="sxs-lookup"><span data-stu-id="f380c-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f380c-601">Die Beispiel-App legt den Wert von `SaveTokens` auf `true` in fest <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="f380c-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f380c-602">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser` der `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="f380c-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f380c-603">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="f380c-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f380c-604">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="f380c-604">How to add additional custom tokens</span></span>

<span data-ttu-id="f380c-605">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von gespeichert wird `SaveTokens` , fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="f380c-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f380c-606">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-606">Creating and adding claims</span></span>

<span data-ttu-id="f380c-607">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f380c-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f380c-608">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="f380c-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f380c-609">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von ableiten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und die abstrakte- <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="f380c-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f380c-610">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f380c-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f380c-611">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="f380c-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="f380c-612">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f380c-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f380c-613">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="f380c-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f380c-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="f380c-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f380c-615">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="f380c-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f380c-616">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f380c-616">Additional resources</span></span>

* <span data-ttu-id="f380c-617">[dotnet/aspnetcore Engineering socialsample-App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): die verknüpfte Beispiel-App befindet sich in der Engineering-Verzweigung des [dotnet/aspnetcore-GitHub-](https://github.com/dotnet/AspNetCore) Repository `master` .</span><span class="sxs-lookup"><span data-stu-id="f380c-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f380c-618">Die `master` Verzweigung enthält Code, der in der aktiven Entwicklung für die nächste Version von ASP.net Core enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="f380c-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f380c-619">Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus (z `release/{X.Y}` . b.).</span><span class="sxs-lookup"><span data-stu-id="f380c-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
