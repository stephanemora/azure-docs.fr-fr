---
title: Pourquoi mettre à jour à la plateforme d’identité Microsoft (v2.0) | Azure
description: Connaître les différences entre le point de terminaison Microsoft identity platform (v2.0) et le point de terminaison v1.0 Azure Active Directory (Azure AD) et découvrez les avantages de la mise à jour vers la version 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7389431212fd34960279aad5321ef2eeb70b7790
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540435"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Pourquoi mettre à jour à la plateforme d’identité Microsoft (v2.0) ?

Lorsque vous développez une nouvelle application, il est important de connaître les différences entre la plateforme d’identité Microsoft (v2.0) et les points de terminaison Azure Active Directory (v1.0). Cet article décrit les principales différences entre les points de terminaison et des limitations existantes pour la plateforme d’identité Microsoft.

> [!NOTE]
> Le point de terminaison Microsoft identity plateforme ne prend pas en charge tous les scénarios Azure AD et les fonctionnalités. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Qui peut se connecter

![Utilisateurs autorisés à se connecter avec les points de terminaison v1.0 et v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* Le point de terminaison v1.0 permet uniquement aux comptes professionnels et scolaires de se connecter à votre application (Azure AD).
* Le point de terminaison Microsoft identity plateforme permet de travail et les comptes scolaires à partir d’Azure AD et les comptes personnels Microsoft (MSA), telles que hotmail.com, outlook.com et msn.com, pour vous connecter.
* Les deux points de terminaison également acceptent des connexions de *[utilisateurs invités](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* d’un répertoire Azure AD pour les applications configurées en tant que *[mono-locataire](single-and-multi-tenant-apps.md)* ou pour *multi-locataire* applications configurées pour pointer vers le point de terminaison spécifique au client (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Le point de terminaison Microsoft identity platform vous permet d’écrire des applications qui acceptent les connexions depuis des comptes Microsoft personnels et comptes professionnels et scolaires. Ceci vous donne la possibilité d’écrire votre application de façon complètement indépendante des comptes. Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités et données supplémentaires seront disponibles pour les comptes professionnels, comme leurs sites SharePoint ou les données de leurs annuaires. Mais pour de nombreuses actions, comme la [lecture du courrier d’un utilisateur](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), vous pouvez utiliser le même code pour accéder à la messagerie, aussi bien pour les comptes professionnels que scolaires.

Point de terminaison de plateforme d’identité de Microsoft, vous pouvez utiliser la bibliothèque d’authentification Microsoft (MSAL) pour accéder au consommateur, enseignement et les mondes enterprise. Le point de terminaison Azure AD v1.0 accepte les connexions uniquement depuis des comptes professionnels et scolaires.

## <a name="incremental-and-dynamic-consent"></a>Consentement incrémentiel et dynamique

Les applications qui utilisent le point de terminaison Azure AD v1.0 doivent spécifier à l’avance les autorisations OAuth 2.0 requises, par exemple :

![Interface utilisateur de l’inscription des autorisations](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Les autorisations qui sont directement définies sur l’inscription d’application sont **statiques**. Bien que les autorisations statiques de l’application définies dans le portail Azure préservent la simplicité du code, elles présentent quelques problèmes potentiels pour les développeurs :

* L’application doit demander toutes les autorisations dont elle est susceptible d’avoir besoin dès la première connexion de l’utilisateur. Ceci peut résulter en une longue liste d’autorisations qui décourage les utilisateurs finaux d’approuver l’accès de l’application lors de la connexion initiale.

* L’application doit connaître au préalable l’ensemble des ressources auxquelles elle est susceptible d’accéder. Il était difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.

Avec le point de terminaison Microsoft identity plateforme, vous pouvez ignorer les autorisations de statiques définies dans les informations de d’inscription d’application dans les autorisations de demande et le portail Azure de façon incrémentielle au lieu de cela, ce qui signifie que la demande d’un ensemble minimal de système d’autorisations dès le départ et augmente plus au fil du temps que le client utilise les fonctionnalités des applications supplémentaires. Pour cela, vous pouvez spécifier les étendues dont votre application a besoin à tout moment en incluant les nouvelles étendues dans le paramètre `scope` quand vous demandez un jeton d’accès, sans qu’il soit nécessaire de les définir au préalable dans les informations d’inscription de l’application. Si l’utilisateur n’a pas encore consenti aux nouvelles étendues ajoutées à la demande, il est invité à donner son consentement seulement aux nouvelles autorisations. Pour plus d’informations, consultez [Autorisations, consentements et étendues](v2-permissions-and-consent.md).

En permettant à l’application de demander des autorisations de façon dynamique grâce au paramètre `scope`, les développeurs maîtrisent totalement l’expérience de vos utilisateurs. Vous pouvez aussi anticiper l’expérience de consentement et demander toutes les autorisations dans une même demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez les demander à l’utilisateur de façon incrémentielle quand il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

Quand il est recueilli pour le compte d’une organisation, le consentement de l’administrateur nécessite toujours les autorisations statiques inscrites pour l’application. Par conséquent, nous vous recommandons de définir ces autorisations pour les applications dans le portail d’inscription des applications si vous avez besoin qu’un administrateur donne son consentement pour le compte de toute l’organisation. Ainsi, les cycles nécessaires à l’administrateur de l’organisation pour configurer l’application sont réduits.

## <a name="scopes-not-resources"></a>Des étendues, pas des ressources

Lorsqu’elle utilise le point de terminaison v1.0, une application peut se comporter comme une **ressource** ou comme un destinataire de jetons. Une ressource peut définir plusieurs **étendues** ou **oAuth2Permissions** qu’elle comprend, permettant ainsi aux applications clientes de demander des jetons à cette ressource pour un ensemble d’étendues donné. Prenez comme exemple de ressource l’API Graph d’Azure AD :

* Identificateur de ressource, ou `AppID URI` : `https://graph.windows.net/`
* Étendues ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, et ainsi de suite.

Cela est vrai pour le point de terminaison Microsoft identity platform. Une application peut toujours se comporter comme une ressource, définir des étendues et être identifiée par un URI. Les applications clientes peuvent toujours demander l’accès à ces étendues. Toutefois, la manière qu’un client demande ces autorisations ont été modifiés.

Pour le point de terminaison v1.0, une demande d’autorisation OAuth 2.0 à Azure AD pouvait ressembler à ceci :

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Ici, le paramètre **resource** indiquait la ressource pour laquelle l’application cliente demandait une autorisation. Azure AD calculait les autorisations requises par l’application en fonction de la configuration statique définie sur le Portail Azure, puis émettait les jetons en conséquence.

Pour les applications utilisant le point de terminaison Microsoft identity plateforme, le même OAuth 2.0 pour autoriser demande ressemble à :

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Ici, le paramètre **scope** indique la ressource et les autorisations pour lesquelles l’application demande une autorisation. La ressource souhaitée est toujours présente dans la demande : elle est englobée dans chacune des valeurs du paramètre d’étendue. Utiliser le paramètre d’étendue de cette manière permet à l’extrémité de plateforme d’identité Microsoft être plus conforme à la spécification OAuth 2.0 et aligne plus étroitement avec les pratiques courantes du secteur. Il permet également pour les applications [consentement incrémentiel](#incremental-and-dynamic-consent) : seules demande des autorisations lors de l’application en a besoin, par opposition à en amont.

## <a name="well-known-scopes"></a>Étendues connues

### <a name="offline-access"></a>Accès hors connexion

Applications à l’aide du point de terminaison Microsoft identity plateforme peuvent nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications - le `offline_access` étendue. Toutes les applications doivent demander cette autorisation si elles doivent accéder aux ressources au nom d’un utilisateur pendant une période prolongée, même si l’utilisateur n’utilise peut-être pas activement l’application donnée. L’étendue `offline_access` apparaît à l’utilisateur dans la boîte de dialogue de consentement **Accéder aux données à tout moment**, que l’utilisateur doit accepter. Demande le `offline_access` autorisation permettra à votre application web recevoir les jetons d’actualisation OAuth 2.0 à partir de l’extrémité de plateforme Microsoft identity. Les jetons d’actualisation ont une durée de vie assez longue, et peuvent être échangés contre les nouveaux jetons d’accès OAuth 2.0 pour des périodes d’accès prolongées.

Si votre application ne sollicite la `offline_access` étendue, elle ne reçoit pas les jetons d’actualisation. Ainsi, quand vous échangez un code d’autorisation dans le flux de code d’autorisation OAuth 2.0, vous recevez seulement en retour un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période de temps (généralement une heure), mais il finit par expirer. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Lors de cette redirection, il peut ou non être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou de consentir une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur OAuth 2.0, `refresh_tokens`, et `access_tokens`, consultez le [référence de protocole plateforme Microsoft identity](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil et e-mail

Historiquement, le plus simple flux OpenID Connect connectez-vous avec la plateforme d’identité Microsoft pouvait fournir beaucoup d’informations sur l’utilisateur dans le résultat *id_token*. Les revendications contenues dans un paramètre id_token peuvent inclure différentes informations utilisateur : son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

Les informations auxquelles l’étendue `openid` permet à votre application d’accéder sont maintenant restreintes. L’étendue `openid` ne l’autorisera qu’à connecter l’utilisateur et à recevoir un identificateur propre à l’application pour l’utilisateur. Si vous voulez obtenir des données personnelles sur l’utilisateur dans votre application, celle-ci doit lui demander des autorisations supplémentaires. Deux nouvelles étendues, `email` et `profile`, vous permettent de le faire.

* L’étendue `email` permet à votre application d’accéder à l’adresse e-mail principale de l’utilisateur via la revendication `email` contenue dans id_token, en supposant que l’utilisateur ait une adresse e-mail utilisable.
* Le `profile` étendue permet à votre application l’accès à toutes les autres informations de base relatives à l’utilisateur, telles que leur nom, nom d’utilisateur privilégié, ID d’objet et ainsi de suite, dans le paramètre id_token.

Ces étendues vous permettent de coder votre application en divulguant le moins possible d’informations : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application. Pour plus d’informations sur ces étendues, consultez [la référence de portée de plateforme Microsoft identity](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Revendications de jetons

Le point de terminaison Microsoft identity plateforme émet un plus petit jeu de revendications dans ses jetons par défaut pour limiter la taille des charges utiles. Si vous avez des applications et services qui ont une dépendance sur une revendication particulière dans un jeton de v1.0 n’est plus disponible par défaut dans un jeton de plate-forme Microsoft identity, envisagez d’utiliser le [revendications facultatives](active-directory-optional-claims.md) fonctionnalité pour inclure cette revendication.

## <a name="limitations"></a>Limites

Il existe quelques restrictions à connaître lors de l’utilisation de plateforme d’identité Microsoft.

Lorsque vous générez des applications qui s’intègrent à la plateforme Microsoft identity, vous devez déterminer si les Microsoft identity plateforme point de terminaison de protocoles d’authentification et répondent à vos besoins. Le point de terminaison v1.0 et la plateforme est toujours intégralement pris en charge et, à certains égards, est plus riche en fonctionnalités que la plateforme d’identité Microsoft. Toutefois, plateforme d’identité Microsoft [présente des avantages significatifs](azure-ad-endpoint-comparison.md) pour les développeurs.

Voici maintenant une recommandation simplifiée pour les développeurs :

* Si vous voulez ou devez prendre en charge des comptes personnels Microsoft dans votre application, ou que vous écrivez une nouvelle application, utilisez la plateforme d’identité Microsoft. Avant cela, comprenez cependant bien les limitations exposées dans cet article.
* Si vous utilisez la migration ou la mise à jour d’une application qui s’appuie sur SAML, vous ne pouvez pas utiliser la plateforme d’identité Microsoft. Au lieu de cela, reportez-vous à la [guide Azure Active Directory v1.0](v1-overview.md).

Le point de terminaison Microsoft identity plateforme va évoluer pour éliminer les restrictions répertoriées ici, afin que vous devez toujours utiliser le point de terminaison Microsoft identity platform. En attendant, utilisez cet article pour déterminer si le point de terminaison Microsoft identity plateforme vous convient. Nous continuerons à mettre à jour de cet article pour refléter l’état actuel du point de terminaison de plateforme d’identité Microsoft. Vérifiez régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de plateforme d’identité Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application

Pour chaque application que vous souhaitez intégrer avec le point de terminaison Microsoft identity plateforme, vous pouvez créer une inscription d’application dans le nouveau [ **inscriptions** expérience](https://aka.ms/appregistrations) dans le portail Azure. Les applications de compte Microsoft existantes ne sont pas compatibles avec le portail, mais toutes les applications Azure AD sont, où et quand ils ont été enregistrés.

Les inscriptions d’applications qui prennent en charge les comptes professionnels et scolaires ont les caractéristiques suivantes :

* Seuls deux secrets d’application sont autorisés par ID d’application.
* Une application qui n’a pas été inscrite dans un locataire peut être gérée seulement par le compte qui l’a inscrite. Elle ne peut pas être partagée avec d’autres développeurs. C’est le cas pour la plupart des applications qui ont été inscrites avec un compte Microsoft personnel dans le portail d’inscription des applications. Si vous souhaitez partager votre inscription d’application avec plusieurs développeurs, inscrire l’application dans un client à l’aide de la nouvelle **inscriptions** section du portail Azure.
* Il existe plusieurs restrictions quant au format de l’URL de redirection autorisé. Pour plus d’informations sur les URL de redirection, consultez la section suivante.

### <a name="restrictions-on-redirect-urls"></a>Restrictions concernant les URL de redirection

Les applications qui sont inscrits pour la plateforme d’identité Microsoft sont limitées à un ensemble limité de valeurs d’URL de redirection. L’URL de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de redirection doivent partager un seul domaine DNS.  Le système d’inscription compare le nom DNS complet de l’URL de redirection existante au nom DNS de l’URL de redirection que vous ajoutez. `http://localhost` est également pris en charge comme URL de redirection.  

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

Vous pouvez ajouter les deux derniers parce qu’ils sont des sous-domaines de la première URL de redirection, contoso.com.

Vous pouvez avoir que 20 URL de réponse pour une application particulière : cette limite s’applique pour tous les types d’application que l’inscription prend en charge (application à page unique (SPA) native client, application web et service).  

Pour savoir comment inscrire une application pour une utilisation avec la plateforme d’identité Microsoft, consultez [inscrire une application à l’aide de la nouvelle expérience d’inscriptions application](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK

Actuellement, la prise en charge de la bibliothèque pour le point de terminaison Microsoft identity plateforme est limitée. Si vous souhaitez utiliser le point de terminaison Microsoft identity plateforme dans une application de production, vous disposez des options suivantes :

* Si vous créez une application web, vous pouvez en toute sécurité utiliser l’intergiciel (middleware) côté serveur à la disposition générale pour la connexion et validation des jetons. Il s’agit notamment du middleware OWIN OpenID Connect pour ASP.NET et du plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent l’intergiciel (middleware) de Microsoft, consultez le [plateforme d’identité Microsoft mise en route](v2-overview.md#getting-started) section.
* Si vous créez une application de bureau ou mobile, vous pouvez utiliser une de bibliothèques d’authentification Microsoft (MSAL). Ces bibliothèques sont à la disposition générale ou dans une version préliminaire pris en charge de production, il est donc sans risque les utiliser dans les applications de production. Pour plus d’informations sur les conditions d’utilisation de la préversion et sur les bibliothèques disponibles, consultez la [documentation de référence sur les bibliothèques d’authentification](reference-v2-libraries.md).
* Pour les plateformes non couvertes par les bibliothèques de Microsoft, vous pouvez intégrer avec le point de terminaison Microsoft identity plateforme par directement envoyer et recevoir des messages de protocole dans votre code d’application. Les protocoles OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
* Enfin, vous pouvez utiliser les bibliothèques open source OpenID Connect et OAuth à intégrer à l’extrémité de plateforme Microsoft identity. Le point de terminaison Microsoft identity plateforme doit être compatible avec de nombreuses bibliothèques de protocole open source sans modification. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [OpenID Connect](https://openid.net/connect/) et [OAuth 2.0](https://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Microsoft identity platform et bibliothèques d’authentification](reference-v2-libraries.md)et la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison Microsoft identity platform.
* Pour référence, le `.well-known` point de terminaison pour le point de terminaison de Microsoft identity plateforme commune est `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Remplacez `common` par votre ID de locataire pour obtenir des données spécifiques à votre locataire.  

### <a name="protocol-changes"></a>Changements de protocole

Le point de terminaison Microsoft identity plateforme ne prend pas en charge SAML ou WS-Federation ; Il prend uniquement en charge OpenID Connect et OAuth 2.0.  Les changements notables apportés aux protocoles OAuth 2.0 par rapport au point de terminaison v1.0 sont : 

* La revendication `email` est retournée si une revendication facultative est configurée **ou** si scope=email a été spécifié dans la demande. 
* Le paramètre `scope` est désormais pris en charge à la place du paramètre `resource`.  
* De nombreuses réponses ont été modifiées pour les rendre plus conforme à la spécification OAuth 2.0, par exemple retourner correctement `expires_in` sous la forme d’un entier au lieu d’une chaîne.  

Pour mieux comprendre l’étendue de la fonctionnalité de protocole pris en charge dans le point de terminaison Microsoft identity plateforme, consultez [référence sur le protocole OpenID Connect et OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrictions SAML

Si vous avez utilisé Active Directory Authentication Library (ADAL) dans les applications Windows, vous pouvez avoir dirigé parti de l’authentification Windows intégrée, qui utilise l’octroi d’assertion Security Assertion Markup Language (SAML). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. L’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison Microsoft identity platform.
