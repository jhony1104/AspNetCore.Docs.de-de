---
title: Externe OAuth-Authentifizierungs Anbieter
author: rick-anderson
description: Externe OAuth-Authentifizierungs Anbieter ermitteln, die mit ASP.net Core-apps arbeiten.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: 438b06dfa55a6b5bdd7b97516005e1f918f7b6ae
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406068"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="a0813-103">Externe OAuth-Authentifizierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="a0813-103">External OAuth authentication providers</span></span>

<span data-ttu-id="a0813-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)und [Valeriy novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="a0813-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="a0813-105">In der folgenden Liste sind allgemeine externe OAuth-Authentifizierungs Anbieter enthalten, die mit ASP.net Core-apps arbeiten.</span><span class="sxs-lookup"><span data-stu-id="a0813-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="a0813-106">Nuget-Pakete von Drittanbietern, z. b. die von [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)verwalteten, können verwendet werden, um die vom ASP.net Core Team implementierten Authentifizierungs Anbieter zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="a0813-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="a0813-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Anweisungen](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="a0813-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="a0813-108">[Instagram](https://www.instagram.com/developer/register/) ([Anweisungen](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="a0813-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="a0813-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Anweisungen](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="a0813-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="a0813-110">[GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Anweisungen](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="a0813-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="a0813-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Anweisungen](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="a0813-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="a0813-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Anweisungen](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="a0813-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="a0813-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Anweisungen](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="a0813-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="a0813-114">[Tasche](https://getpocket.com/developer/apps/new) ([Anweisungen](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="a0813-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="a0813-115">[Flickr](https://www.flickr.com/services/apps/create) ([Anweisungen](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="a0813-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="a0813-116">[Dribble](https://dribbble.com/signup) ([Anweisungen](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="a0813-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="a0813-117">[Vimeo](https://vimeo.com/join) ([Anweisungen](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="a0813-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="a0813-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Anweisungen](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="a0813-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="a0813-119">[VK](https://vk.com/apps?act=manage) ([Anweisungen](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="a0813-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
