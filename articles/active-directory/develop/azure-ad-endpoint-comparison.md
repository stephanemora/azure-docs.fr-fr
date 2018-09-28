---
title: Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0 | Microsoft Docs
description: Découvrez les différences entre les points de terminaison Azure AD v2.0 et v1.0.
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: andret
ms.reviewer: hirsin, celested
ms.custom: aaddev
ms.openlocfilehash: 02c7edc84d2ac3a91c33d8f266d022db5cd5cb40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948951"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-v10-endpoint"></a>Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0

Lorsque vous développez une nouvelle application, vous devez connaître les différences entre les points de terminaison v1.0 et v2.0. Les principales différences sont répertoriées ici, accompagnées de certaines limitations qui s’appliquent au point de terminaison v2.0.

> [!NOTE]
> Les scénarios et les fonctionnalités d’Azure AD ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](#limitations).

## <a name="who-can-sign-in"></a>Utilisateurs autorisés à se connecter

![Utilisateurs autorisés à se connecter avec les points de terminaison v1.0 et v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* Le point de terminaison v1.0 permet uniquement aux comptes professionnels et scolaires de se connecter à votre application (Azure AD).

* Le point de terminaison v2.0 permet aux comptes professionnels et scolaires d’Azure Active Directory et aux comptes personnels (MSA) (hotmail.com, outlook.com, msn.com) de se connecter.

* Tous deux (v1.0 et v2.0) acceptent des connexions d’*[utilisateurs invités](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* issus d’un répertoire Azure AD pour les applications configurées en tant que *[mono-locataire](single-and-multi-tenant-apps.md)* ou pour les applications *multi-locataire* configurées de manière à pointer vers le point de terminaison spécifique au locataire (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Le point de terminaison v2.0 vous permet d’écrire des applications qui acceptent des connexions à partir de comptes professionnels et scolaires, ce qui vous donne la possibilité d’écrire votre application sans vous soucier du type de compte. Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités et données supplémentaires seront disponibles pour les comptes professionnels (leurs sites SharePoint ou les données du répertoire, par exemple). Mais pour de nombreuses actions, comme la [lecture du courrier d’un utilisateur](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), vous pouvez utiliser le même code pour accéder à la messagerie, aussi bien pour les comptes professionnels que scolaires.

Pour le point de terminaison v2.0, vous pouvez utiliser une bibliothèque unique (MSAL) pour accéder aux différents univers (consommation, scolaire ou professionnel).

 Le point de terminaison Azure AD v1.0 accepte les connexions uniquement depuis des comptes professionnels et scolaires.

## <a name="incremental-and-dynamic-consent"></a>Consentement incrémentiel et dynamique

Les applications qui utilisent le point de terminaison Azure AD v1.0 doivent spécifier à l’avance les autorisations OAuth 2.0 requises, par exemple :

![Interface utilisateur de l’inscription des autorisations](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Les autorisations qui sont directement définies sur l’inscription d’application sont **statiques**. Bien que les autorisations statiques de l’application soient définies sur le Portail Azure et préservent la simplicité du code, elles présentent quelques problèmes pour les développeurs :

* Au moment de sa création, l’application doit connaître toutes les autorisations dont elle aura besoin. L’ajout d’autorisations au fil du temps était un processus difficile.

* L’application doit connaître au préalable l’ensemble des ressources auxquelles elle est susceptible d’avoir accès. Il était difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.

* L’application doit demander toutes les autorisations dont elle est susceptible d’avoir besoin dès la première connexion de l’utilisateur. Dans certains cas, il en résultait une longue liste d’autorisations, qui dissuadait les utilisateurs finaux d’approuver l’accès de l’application à la connexion initiale.

Avec le point de terminaison v2.0, vous pouvez ignorer les autorisations statiques qui sont définies dans les informations de l’inscription d’application du Portail Azure. Vous pouvez spécifier les autorisations dont votre application a besoin de façon **dynamique** lors de son exécution, pendant une utilisation normale, indépendamment des autorisations statiques qui sont définies dans les informations de l’inscription d’application.

Pour ce faire, vous pouvez spécifier les étendues dont votre application a besoin à des moments précis dans votre temps d'application. Vous devez inclure les nouvelles étendues dans le paramètre `scope` lorsque vous demandez un jeton d’accès (vous n’avez pas besoin de les définir au préalable dans les informations de l’inscription d’application).

Si l’utilisateur n’a pas encore accepté les nouvelles étendues ajoutées à la requête, il est invité à donner son consentement uniquement pour les nouvelles autorisations. Pour en savoir plus, consultez les [autorisations, consentements et étendues](v2-permissions-and-consent.md).

En permettant à l’application de demander des autorisations de façon dynamique grâce au paramètre `scope`, les développeurs maîtrisent totalement l’expérience de vos utilisateurs. Si vous le souhaitez, vous pouvez également choisir d’anticiper l’expérience de consentement et récolter toutes les autorisations dans une demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez choisir de rassembler celles de l’utilisateur de façon incrémentielle lorsqu’il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

Remarque : lorsqu’il est recueilli pour le compte d’une organisation, le consentement administrateur utilise toujours les autorisations statiques qui sont enregistrées pour l’application. Par conséquent, nous vous recommandons de définir ces autorisations à l’aide du point de terminaison v2.0 si vous souhaitez qu’un administrateur donne son consentement pour le compte d’une organisation. Ainsi, les cycles nécessaires à l’administrateur de l’organisation pour configurer l’application sont réduits.

## <a name="scopes-not-resources"></a>Des étendues, pas des ressources

Lorsqu’elle utilise le point de terminaison v1.0, une application peut se comporter comme une **ressource** ou comme un destinataire de jetons. Une ressource peut définir plusieurs **étendues** ou **oAuth2Permissions** qu’elle comprend, permettant ainsi aux applications clientes de demander des jetons pour cette ressource pour un ensemble d’étendues donné. Prenez comme exemple de ressource l’API Graph d’Azure AD :

* Identificateur de ressource, ou `AppID URI` : `https://graph.windows.net/`

* Étendues ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, et ainsi de suite.

Tout ceci est vrai pour le point de terminaison v2.0. Une application peut toujours se comporter comme une ressource, définir des étendues et être identifiée par un URI. Les applications clientes peuvent toujours demander l’accès à ces étendues. Toutefois, la manière dont le client demande ces autorisations a changé. Pour le point de terminaison v1.0, une demande d’autorisation OAuth 2.0 à Azure AD pouvait ressembler à ceci :

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

où le paramètre de **ressource** indiquait la ressource pour laquelle l’application cliente demandait une autorisation. Azure AD calculait les autorisations requises par l’application en fonction de la configuration statique définie sur le Portail Azure, puis émettait les jetons en conséquence. Pour les applications utilisant le point de terminaison v2.0, cette même demande d’autorisation OAuth 2.0 ressemble à ceci :

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

où le paramètre d’**étendue** indique la ressource et les autorisations pour lesquelles l’application demande une autorisation. La ressource souhaitée est toujours présente dans la demande ; elle est simplement englobée dans chacune des valeurs du paramètre d’étendue. L’utilisation du paramètre d’étendue de cette manière permet au point de terminaison v2.0 d’être plus conforme à la spécification OAuth 2.0, et elle est en harmonie plus étroite avec les pratiques courantes du secteur. Elle permet également aux applications d’effectuer un [consentement incrémentiel](#incremental-and-dynamic-consent), qui est décrit précédemment.

## <a name="well-known-scopes"></a>Étendues connues

### <a name="offline-access"></a>Accès hors connexion

Les applications qui utilisent le point de terminaison v2.0 peuvent nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications : l’étendue `offline_access`. Toutes les applications doivent demander cette autorisation si elles doivent accéder aux ressources au nom d’un utilisateur pendant une période prolongée, même si l’utilisateur n’utilise peut-être pas activement l’application donnée. L’étendue `offline_access` apparaît à l’utilisateur dans la boîte de dialogue de consentement **Accéder aux données à tout moment**, que l’utilisateur doit accepter. Demander l’autorisation `offline_access` permet à votre application web de recevoir les jetons d’actualisation OAuth 2.0 du point de terminaison v2.0. Les jetons d’actualisation ont une durée de vie assez longue, et peuvent être échangés contre les nouveaux jetons d’accès OAuth 2.0 pour des périodes d’accès prolongées.

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas de jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans un flux de code d’autorisation OAuth 2.0, vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période (généralement une heure), avant d’arriver à expiration. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant cette redirection, il peut être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou d’accepter une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur OAuth 2.0, `refresh_tokens`, et `access_tokens`, consultez les [références sur les protocoles v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil et e-mail

Historiquement, le flux de connexion OpenID Connect le plus simple avec Azure AD fournissait de nombreuses informations sur l’utilisateur dans l’élément *id_token* qui en résultait. Les revendications contenues dans un paramètre *id_token* peuvent inclure différentes informations utilisateur : son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

Les informations auxquelles l’étendue `openid` permet à votre application d’accéder sont maintenant restreintes. L’étendue `openid` ne l’autorisera qu’à connecter l’utilisateur et à recevoir un identificateur propre à l’application pour l’utilisateur. Si vous souhaitez obtenir des données personnelles sur l’utilisateur dans votre application, celle-ci devra lui demander des autorisations supplémentaires. Deux nouvelles étendues, `email` et `profile`, vous permettront de le faire.

L’étendue `email` permet à votre application d’accéder à l’adresse e-mail principale de l’utilisateur par le biais de la revendication `email` contenue dans id_token. L’étendue `profile` permet à votre application d’accéder à toutes les autres informations de base sur l’utilisateur (nom, nom d’utilisateur par défaut, ID objet, etc.).

Vous pouvez ainsi coder votre application en en divulguant le moins possible : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application. Pour plus d’informations sur ces étendues, voir [Référence sur les étendues v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Demandes de jetons

Les revendications des jetons émis par le point de terminaison v2.0 ne seront pas identiques aux jetons émis par les points de terminaison Azure AD mis à la disposition générale. Les applications qui passent au nouveau service ne doivent pas partir du principe que chaque revendication sera présente dans id_tokens ou access_tokens. Pour plus d’informations sur les différents types de jetons utilisés dans le point de terminaison v2.0, consultez les articles de référence au [jeton d'accès](access-tokens.md) et à [`id_token`](id-tokens.md).

## <a name="limitations"></a>Limites

Il existe quelques restrictions à connaître lors de l’utilisation de v2.0.

Lorsque vous créez des applications qui s’intègrent dans la plateforme d’identité Microsoft, vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins. Le point de terminaison et la plateforme v1.0 sont toujours intégralement pris en charge. À certains égards, ils sont plus riches en fonctionnalités que la v2.0. Toutefois, la v2.0 [présente des avantages significatifs](azure-ad-endpoint-comparison.md) pour les développeurs.

Voici une suggestion simplifiée pour les développeurs à ce stade :

* Si vous devez prendre en charge des comptes Microsoft personnels dans votre application, utilisez la v2.0. Mais avant cela, tenez compte des limitations abordées dans cet article.

* Si votre application ne doit prendre en charge que des comptes Microsoft professionnels et scolaires, n’utilisez pas la v2.0. Consultez plutôt le [guide relatif à la v1.0](azure-ad-developers-guide.md).

Le point de terminaison v2.0 va évoluer et les restrictions répertoriées ici seront éliminées. Ainsi, vous n’aurez qu’à utiliser le point de terminaison v2.0. En attendant, cet article est là pour vous aider à déterminer si le point de terminaison v2.0 répond à vos besoins. Nous continuerons à mettre à jour cet article pour refléter l’état actuel du point de terminaison v2.0. Consultez-le régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de la version 2.0.

### <a name="restrictions-on-app-types"></a>Restrictions concernant les types d’applications

Actuellement, les types d’applications suivants ne sont pas pris en charge par le point de terminaison v2.0. Pour obtenir une description des types d’applications pris en charge, consultez l’article relatif aux [types d’application dans la v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>API Web autonome

Vous pouvez utiliser le point de terminaison v2.0 pour [générer une API web sécurisée avec OAuth 2.0](v2-app-types.md#web-apis). Toutefois, cette API web peut recevoir uniquement les jetons d’une application ayant le même ID d’application. Vous ne pouvez pas accéder à une API web à partir d’un client qui a un ID d’application différent. Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour savoir comment créer une API web qui accepte des jetons d’un client ayant le même ID d’application, consultez les exemples d’API web de point de terminaison v2.0 de la section [Prise en main v2.0](v2-overview.md#getting-started).

### <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application

À l’heure actuelle, pour chaque application que vous souhaitez intégrer au point de terminaison v2.0, vous devez créer une inscription d’application dans le nouveau [portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Les applications de compte Microsoft ou Azure AD existantes ne sont pas compatibles avec le point de terminaison v2.0. Les applications qui sont inscrites dans un portail autre que le portail d’inscription des applications ne sont pas compatibles avec le point de terminaison v2.0.

De plus, les inscriptions d’applications que vous créez dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ont les caractéristiques suivantes :

* Seuls deux secrets d’application sont autorisés par ID d’application.

* Une application inscrite par un utilisateur dans un compte Microsoft personnel ne peut être affichée et gérée que par un compte de développeur. Elle ne peut pas être partagée entre plusieurs développeurs. Si vous souhaitez partager votre inscription d’application avec plusieurs développeurs, vous pouvez créer l’application en vous connectant au portail d’inscription avec un compte Azure AD.

* Il existe plusieurs restrictions quant au format de l’URL de redirection autorisé. Pour plus d’informations sur les URL de redirection, consultez la section suivante.

### <a name="restrictions-on-redirect-urls"></a>Restrictions concernant les URL de redirection

Les applications inscrites dans le portail d’inscription des applications sont limitées à un jeu restreint de valeurs d’URL de redirection. L’URL de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URL de redirection doivent partager un seul domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web contenant l’une de ces URL de redirection :

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URL de redirection existante au nom DNS de l’URL de redirection que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

* Le nom DNS complet de la nouvelle URL de redirection ne correspond pas au nom DNS de l’URL de redirection existante.

* Le nom DNS complet de la nouvelle URL de redirection n’est pas un sous-domaine de l’URL de redirection existante.

Par exemple, si l’application a cette URL de redirection :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous souhaitez que votre application utilise les URL de redirection `login-east.contoso.com` et `login-west.contoso.com`, vous devez ajouter ces URL de redirection dans cet ordre :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Vous pouvez ajouter les deux dernières, car ce sont des sous-domaines de la première URL de redirection, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Notez également qu’une même application ne peut avoir que 20 URL de réponse.

Pour savoir comment inscrire une application dans le portail d’inscription des applications, consultez [Inscription d’une application avec le point de terminaison v2.0](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK

Actuellement, la prise en charge des bibliothèques pour le point de terminaison v2.0 est limitée. Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

* Si vous générez une application web, vous pouvez en toute sécurité utiliser le middleware Microsoft mis à la disposition générale côté serveur pour vous connecter et procéder à la validation des jetons. Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et au plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent le middleware Microsoft, consultez la section [Prise en main v2.0](v2-overview.md#getting-started).

* Si vous créez une application mobile ou de bureau, vous pouvez utiliser l’une de nos bibliothèques d’authentification Microsoft (MSAL). Bien qu’il s’agisse de versions préliminaires, ces bibliothèques sont utilisables dans des applications de production. Pour plus d’informations sur les conditions d’utilisation de la préversion et sur les bibliothèques disponibles, consultez la [documentation de référence sur les bibliothèques d’authentification](reference-v2-libraries.md).

* Vous pouvez intégrer les plateformes non couvertes par les bibliothèques Microsoft, dans le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans le code de votre application. Les protocoles v2.0 OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.

* Pour finir, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0. Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source sans modification majeure. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Azure Active Directory v2.0 et bibliothèques d’authentification](reference-v2-libraries.md), ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.

* Pour référence, le point de terminaison `.well-known` pour le point de terminaison v2.0 commun est `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.  Remplacez `common` par votre ID de locataire pour obtenir des données spécifiques à votre locataire.  

### <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles

Le point de terminaison 2.0 ne prend pas en charge SAML ou WS-Federation, mais uniquement Open ID Connect et OAuth 2.0. Certaines fonctionnalités des protocoles OAuth n’ont pas été intégrées dans le point de terminaison v2.0.

Les fonctionnalités de protocole suivantes *ne sont pas disponibles* dans le point de terminaison v2.0 :

* Actuellement, la revendication `email` est retournée uniquement si une revendication facultative est configurée, et si scope=email a été spécifié dans la requête. Toutefois, ce comportement va changer avec la mise à jour du point de terminaison v2.0 en vue d’une meilleure conformité aux nomes Open ID Connect et OAuth 2.0.

* Le point de terminaison v2.0 ne prend pas en charge l’émission de revendications de rôle ou de groupe dans les jetons d’ID.

* Le point de terminaison v2.0 ne prend pas en charge l’[octroi des informations de mot de passe du propriétaire de la ressource OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3).

De plus, il ne prend en charge aucun protocole SAML ou WS-Federation.

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [référence sur les protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrictions SAML

Si vous avez utilisé la bibliothèque ADAL (Active Directory Authentication Library) dans des applications Windows, vous avez peut-être tiré parti de l’authentification intégrée Windows, qui utilise l’octroi d’assertions SAML (Security Assertion Markup Language). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. À l’heure actuelle, l’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison v2.0.
