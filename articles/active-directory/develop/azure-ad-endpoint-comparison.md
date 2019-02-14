---
title: Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0 | Microsoft Docs
description: Découvrez les différences entre les points de terminaison Azure AD v2.0 et v1.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075df3d0834d70b8deb7c224c3f6b1cceb46fd7a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185959"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0

Quand vous développez une nouvelle application, vous devez connaître les différences entre les points de terminaison Azure Active Directory (Azure AD) v1.0 et v2.0. Cet article décrit les principales différences entre les points de terminaison et certains limitations existantes pour le point de terminaison v2.0.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge tous les scénarios et fonctionnalités d’Azure AD. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](#limitations).

## <a name="who-can-sign-in"></a>Qui peut se connecter

![Utilisateurs autorisés à se connecter avec les points de terminaison v1.0 et v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Le point de terminaison v1.0 permet uniquement aux comptes professionnels et scolaires de se connecter à votre application (Azure AD).
* Le point de terminaison v2.0 permet aux comptes professionnels et scolaires d’Azure AD ainsi qu’aux comptes Microsoft personnels (MSA), comme hotmail.com, outlook.com et msn.com, de se connecter.
* Tous deux (v1.0 et v2.0) acceptent des connexions d’*[utilisateurs invités](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* issus d’un répertoire Azure AD pour les applications configurées en tant que *[mono-locataire](single-and-multi-tenant-apps.md)* ou pour les applications *multi-locataire* configurées de manière à pointer vers le point de terminaison spécifique au locataire (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Le point de terminaison v2.0 vous permet d’écrire des applications qui acceptent les connexions depuis des comptes Microsoft personnels, et des comptes professionnels et scolaires. Ceci vous donne la possibilité d’écrire votre application de façon complètement indépendante des comptes. Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités et données supplémentaires seront disponibles pour les comptes professionnels, comme leurs sites SharePoint ou les données de leurs annuaires. Mais pour de nombreuses actions, comme la [lecture du courrier d’un utilisateur](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), vous pouvez utiliser le même code pour accéder à la messagerie, aussi bien pour les comptes professionnels que scolaires.

Pour le point de terminaison v2.0, vous pouvez utiliser la bibliothèque MSAL (Microsoft Authentication Library) pour accéder aux différents univers (consommation, scolaire ou professionnel). Le point de terminaison Azure AD v1.0 accepte les connexions uniquement depuis des comptes professionnels et scolaires.

## <a name="incremental-and-dynamic-consent"></a>Consentement incrémentiel et dynamique

Les applications qui utilisent le point de terminaison Azure AD v1.0 doivent spécifier à l’avance les autorisations OAuth 2.0 requises, par exemple :

![Interface utilisateur de l’inscription des autorisations](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Les autorisations qui sont directement définies sur l’inscription d’application sont **statiques**. Bien que les autorisations statiques de l’application définies dans le portail Azure préservent la simplicité du code, elles présentent quelques problèmes potentiels pour les développeurs :

* L’application doit demander toutes les autorisations dont elle est susceptible d’avoir besoin dès la première connexion de l’utilisateur. Ceci peut résulter en une longue liste d’autorisations qui décourage les utilisateurs finaux d’approuver l’accès de l’application lors de la connexion initiale.

* L’application doit connaître au préalable l’ensemble des ressources auxquelles elle est susceptible d’accéder. Il était difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.

Avec le point de terminaison v2.0, vous pouvez ignorer les autorisations statiques définies dans les informations d’inscription de l’application du portail Azure et demander à la place des autorisations de façon incrémentielle, ce qui signifie demander dès le départ un ensemble minimal d’autorisations, puis d’en demander plus au fil du temps dès lors que le client utilise des fonctionnalités supplémentaires de l’application. Pour cela, vous pouvez spécifier les étendues dont votre application a besoin à tout moment en incluant les nouvelles étendues dans le paramètre `scope` quand vous demandez un jeton d’accès, sans qu’il soit nécessaire de les définir au préalable dans les informations d’inscription de l’application. Si l’utilisateur n’a pas encore consenti aux nouvelles étendues ajoutées à la demande, il est invité à donner son consentement seulement aux nouvelles autorisations. Pour plus d’informations, consultez [Autorisations, consentements et étendues](v2-permissions-and-consent.md).

En permettant à l’application de demander des autorisations de façon dynamique grâce au paramètre `scope`, les développeurs maîtrisent totalement l’expérience de vos utilisateurs. Vous pouvez aussi anticiper l’expérience de consentement et demander toutes les autorisations dans une même demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez les demander à l’utilisateur de façon incrémentielle quand il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

Quand il est recueilli pour le compte d’une organisation, le consentement de l’administrateur nécessite toujours les autorisations statiques inscrites pour l’application. Par conséquent, nous vous recommandons de définir ces autorisations pour les applications dans le portail d’inscription des applications si vous avez besoin qu’un administrateur donne son consentement pour le compte de toute l’organisation. Ainsi, les cycles nécessaires à l’administrateur de l’organisation pour configurer l’application sont réduits.

## <a name="scopes-not-resources"></a>Des étendues, pas des ressources

Lorsqu’elle utilise le point de terminaison v1.0, une application peut se comporter comme une **ressource** ou comme un destinataire de jetons. Une ressource peut définir plusieurs **étendues** ou **oAuth2Permissions** qu’elle comprend, permettant ainsi aux applications clientes de demander des jetons à cette ressource pour un ensemble d’étendues donné. Prenez comme exemple de ressource l’API Graph d’Azure AD :

* Identificateur de ressource, ou `AppID URI` : `https://graph.windows.net/`
* Étendues ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, et ainsi de suite.

Ceci est vrai pour le point de terminaison v2.0. Une application peut toujours se comporter comme une ressource, définir des étendues et être identifiée par un URI. Les applications clientes peuvent toujours demander l’accès à ces étendues. Toutefois, la manière dont le client demande ces autorisations a changé. 

Pour le point de terminaison v1.0, une demande d’autorisation OAuth 2.0 à Azure AD pouvait ressembler à ceci :

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Ici, le paramètre **resource** indiquait la ressource pour laquelle l’application cliente demandait une autorisation. Azure AD calculait les autorisations requises par l’application en fonction de la configuration statique définie sur le Portail Azure, puis émettait les jetons en conséquence. 

Pour les applications utilisant le point de terminaison v2.0, cette même demande d’autorisation OAuth 2.0 ressemble à ceci :

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Ici, le paramètre **scope** indique la ressource et les autorisations pour lesquelles l’application demande une autorisation. La ressource souhaitée est toujours présente dans la demande : elle est englobée dans chacune des valeurs du paramètre d’étendue. L’utilisation du paramètre d’étendue de cette manière permet au point de terminaison v2.0 d’être plus conforme à la spécification OAuth 2.0, et elle est en harmonie plus étroite avec les pratiques courantes du secteur. Il permet également aux applications d’effectuer un [consentement incrémentiel](#incremental-and-dynamic-consent) : demander des autorisations seulement quand l’application en a besoin, et non pas au préalable.

## <a name="well-known-scopes"></a>Étendues connues

### <a name="offline-access"></a>Accès hors connexion

Les applications qui utilisent le point de terminaison v2.0 peuvent nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications : l’étendue `offline_access`. Toutes les applications doivent demander cette autorisation si elles doivent accéder aux ressources au nom d’un utilisateur pendant une période prolongée, même si l’utilisateur n’utilise peut-être pas activement l’application donnée. L’étendue `offline_access` apparaît à l’utilisateur dans la boîte de dialogue de consentement **Accéder aux données à tout moment**, que l’utilisateur doit accepter. Demander l’autorisation `offline_access` permet à votre application web de recevoir les jetons d’actualisation OAuth 2.0 du point de terminaison v2.0. Les jetons d’actualisation ont une durée de vie assez longue, et peuvent être échangés contre les nouveaux jetons d’accès OAuth 2.0 pour des périodes d’accès prolongées.

Si votre application ne demande pas l’étendue `offline_access`, elle ne reçoit pas de jetons d’actualisation. Ainsi, quand vous échangez un code d’autorisation dans le flux de code d’autorisation OAuth 2.0, vous recevez seulement en retour un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période de temps (généralement une heure), mais il finit par expirer. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Lors de cette redirection, il peut ou non être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou de consentir une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur OAuth 2.0, `refresh_tokens`, et `access_tokens`, consultez les [références sur les protocoles v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil et e-mail

Historiquement, le flux de connexion OpenID Connect le plus simple avec Azure AD fournissait de nombreuses informations sur l’utilisateur dans l’élément *id_token* qui en résultait. Les revendications contenues dans un paramètre id_token peuvent inclure différentes informations utilisateur : son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

Les informations auxquelles l’étendue `openid` permet à votre application d’accéder sont maintenant restreintes. L’étendue `openid` ne l’autorisera qu’à connecter l’utilisateur et à recevoir un identificateur propre à l’application pour l’utilisateur. Si vous voulez obtenir des données personnelles sur l’utilisateur dans votre application, celle-ci doit lui demander des autorisations supplémentaires. Deux nouvelles étendues, `email` et `profile`, vous permettent de le faire.

* L’étendue `email` permet à votre application d’accéder à l’adresse e-mail principale de l’utilisateur via la revendication `email` contenue dans id_token, en supposant que l’utilisateur ait une adresse e-mail utilisable. 
* L’étendue `profile` permet à votre application d’accéder à toutes les autres informations de base sur l’utilisateur contenues dans id-token, comme le nom, le nom d’utilisateur par défaut, l’ID d’objet, etc.

Ces étendues vous permettent de coder votre application en divulguant le moins possible d’informations : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application. Pour plus d’informations sur ces étendues, voir [Référence sur les étendues v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Revendications de jetons

Le point de terminaison v2.0 émet un ensemble plus restreint de revendications dans ses jetons par défaut, pour limiter la taille des charges utiles. Si vous avez des applications et des services qui ont une dépendance d’une revendication particulière dans un jeton v1.0 qui n’est plus fourni par défaut dans un jeton v2.0, envisagez d’utiliser la fonctionnalité des [revendications facultatives](active-directory-optional-claims.md) pour inclure cette revendication.

## <a name="limitations"></a>Limites

Il existe quelques restrictions à connaître lors de l’utilisation de v2.0.

Lorsque vous créez des applications qui s’intègrent dans la plateforme d’identité Microsoft, vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins. Le point de terminaison et la plateforme v1.0 sont toujours intégralement pris en charge. À certains égards, ils sont plus riches en fonctionnalités que la v2.0. Toutefois, la v2.0 [présente des avantages significatifs](azure-ad-endpoint-comparison.md) pour les développeurs.

Voici une suggestion simplifiée pour les développeurs à ce stade :

* Si vous voulez ou si vous devez prendre en charge des comptes personnels Microsoft dans votre application, ou si vous écrivez une nouvelle application, utilisez v2.0. Avant cela, comprenez cependant bien les limitations exposées dans cet article.
* Si vous migrez ou que vous mettez à jour une application qui s’appuie sur SAML, vous ne pouvez pas utiliser v2.0. Consultez plutôt le [guide relatif à la v1.0](v1-overview.md).

Le point de terminaison v2.0 va évoluer et les restrictions listées ici seront éliminées. Ainsi, vous pourrez toujours utiliser le point de terminaison v2.0. En attendant, cet article est là pour vous aider à déterminer si le point de terminaison v2.0 répond à vos besoins. Nous continuerons à mettre à jour cet article pour refléter l’état actuel du point de terminaison v2.0. Consultez-le régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de la version 2.0.

### <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application

Pour chaque application que vous voulez intégrer au point de terminaison v2.0, vous pouvez créer une inscription d’application dans le [portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Vous pouvez aussi inscrire une application avec l’expérience [**Inscriptions d’applications (préversion)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) dans le portail Azure. Les applications de compte Microsoft existantes ne sont pas compatibles avec la préversion du portail, mais toutes les applications AAD le sont, quel que soit l’endroit et le moment où elles ont été inscrites. 

Les inscriptions d’applications qui prennent en charge les comptes professionnels et scolaires ont les caractéristiques suivantes :

* Seuls deux secrets d’application sont autorisés par ID d’application.
* Une application qui n’a pas été inscrite dans un locataire peut être gérée seulement par le compte qui l’a inscrite. Elle ne peut pas être partagée avec d’autres développeurs. C’est le cas pour la plupart des applications qui ont été inscrites avec un compte Microsoft personnel dans le portail d’inscription des applications. Si vous voulez partager votre inscription d’application avec plusieurs développeurs, inscrivez l’application dans un locataire en utilisant la section **Inscriptions d’applications (préversion)** du portail Azure.
* Il existe plusieurs restrictions quant au format de l’URL de redirection autorisé. Pour plus d’informations sur les URL de redirection, consultez la section suivante.

### <a name="restrictions-on-redirect-urls"></a>Restrictions concernant les URL de redirection

Les applications inscrites pour v2.0 sont limitées à un jeu restreint de valeurs d’URL de redirection. L’URL de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de redirection doivent partager un seul domaine DNS.  Le système d’inscription compare le nom DNS complet de l’URL de redirection existante au nom DNS de l’URL de redirection que vous ajoutez. `http://localhost` est également pris en charge comme URL de redirection.  

La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

* Le nom DNS complet de la nouvelle URL de redirection ne correspond pas au nom DNS de l’URL de redirection existante.
* Le nom DNS complet de la nouvelle URL de redirection n’est pas un sous-domaine de l’URL de redirection existante.

#### <a name="example-1"></a>Exemple 1

Si l’application a une URL de redirection `https://login.contoso.com`, vous pouvez ajouter une URL de redirection où le nom DNS correspond exactement, comme dans l’exemple suivant :

`https://login.contoso.com/new`

Vous pouvez aussi faire référence à un sous-domaine DNS de login.contoso.com, comme dans l’exemple suivant :

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exemple 2

Si vous voulez que votre application utilise les URL de redirection `login-east.contoso.com` et `login-west.contoso.com`, vous devez ajouter ces URL de redirection dans l’ordre suivant :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Vous pouvez ajouter les deux dernières, car ce sont des sous-domaines de la première URL de redirection, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Notez également que vous pouvez avoir seulement 20 URL de réponse pour une application donnée : cette limite s’applique à tous les types d’application pris en charge par l’inscription (application monopage, client natif, application web et service).  

Pour savoir comment inscrire une application pour une utilisation avec v2.0, consultez ces guides de démarrage rapide :

* [Inscrire une application avec le portail d’inscription d’applications](quickstart-v2-register-an-app.md)
* [Inscrire une application avec l’expérience Inscriptions d’applications (préversion)](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK

Actuellement, la prise en charge des bibliothèques pour le point de terminaison v2.0 est limitée. Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

* Si vous créez une application web, vous pouvez utiliser sans problème le middleware (intergiciel) côté serveur en disponibilité générale pour vous connecter et procéder à la validation des jetons. Il s’agit notamment du middleware OWIN OpenID Connect pour ASP.NET et du plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent le middleware Microsoft, consultez la section [Prise en main v2.0](v2-overview.md#getting-started).
* Si vous créez une application mobile ou pour poste de travail, vous pouvez utiliser une des bibliothèques d’authentification Microsoft (MSAL). Bien qu’il s’agisse de versions préliminaires, ces bibliothèques sont utilisables dans des applications de production. Pour plus d’informations sur les conditions d’utilisation de la préversion et sur les bibliothèques disponibles, consultez la [documentation de référence sur les bibliothèques d’authentification](reference-v2-libraries.md).
* Vous pouvez intégrer les plateformes non couvertes par les bibliothèques Microsoft, dans le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans le code de votre application. Les protocoles v2.0 OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
* Enfin, vous pouvez utiliser les bibliothèques open source OpenID Connect et OAuth pour l’intégration au point de terminaison v2.0. Le point de terminaison v2.0 doit normalement être compatible sans modifications avec de nombreuses bibliothèques de protocoles open source. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [OpenID Connect](https://openid.net/connect/) et [OAuth 2.0](https://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Azure Active Directory v2.0 et bibliothèques d’authentification](reference-v2-libraries.md), ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.
* Pour référence, le point de terminaison `.well-known` pour le point de terminaison v2.0 commun est `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Remplacez `common` par votre ID de locataire pour obtenir des données spécifiques à votre locataire.  

### <a name="protocol-changes"></a>Changements de protocole

Le point de terminaison v2.0 ne prend pas en charge SAML ou WS-Federation ; il prend en charge uniquement OpenID Connect et OAuth 2.0.  Les changements notables apportés aux protocoles OAuth 2.0 par rapport au point de terminaison v1.0 sont : 

* La revendication `email` est retournée si une revendication facultative est configurée **ou** si scope=email a été spécifié dans la demande. 
* Le paramètre `scope` est désormais pris en charge à la place du paramètre `resource`.  
* De nombreuses réponses ont été modifiées pour les rendre plus conforme à la spécification OAuth 2.0, par exemple retourner correctement `expires_in` sous la forme d’un entier au lieu d’une chaîne.  

Pour mieux comprendre l’étendue des fonctionnalités de protocole prises en charge dans le point de terminaison v2.0, consultez [Informations de référence sur les protocoles OpenID Connect et OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrictions SAML

Si vous avez utilisé la bibliothèque ADAL (Active Directory Authentication Library) dans des applications Windows, vous avez peut-être tiré parti de l’authentification intégrée Windows, qui utilise l’octroi d’assertions SAML (Security Assertion Markup Language). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. L’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison v2.0.
