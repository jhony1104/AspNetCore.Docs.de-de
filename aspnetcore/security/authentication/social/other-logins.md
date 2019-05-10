---
title: Externe OAuth-Authentifizierungsanbieter
author: rick-anderson
description: Entdecken Sie externe OAuth-Authentifizierungsanbieter, die mit ASP.NET Core-apps funktionieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/otherlogins
ms.openlocfilehash: b69c366ec1bf12ccf434991fc8a79eaf8c09da3d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897507"
---
# <a name="external-oauth-authentication-providers"></a>Externe OAuth-Authentifizierungsanbieter

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), und [Valeriy Novytskyy](https://github.com/01binary)

Die folgende Liste enthält allgemeine externe OAuth-Authentifizierungsanbieter, die mit ASP.NET Core-apps funktionieren. Drittanbieter-NuGet-Pakete, z. B. diejenigen, die von verwaltet [Aspnet-Contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), können verwendet werden, um die durch das ASP.NET Core-Team implementiert Authentifizierungsanbieter zu ergänzen.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([Anweisungen](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Anweisungen](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Anweisungen](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Anweisungen](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([Anweisungen](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Anweisungen](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([Anweisungen](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([Anweisungen](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([Anweisungen](http://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([Anweisungen](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([Anweisungen](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
