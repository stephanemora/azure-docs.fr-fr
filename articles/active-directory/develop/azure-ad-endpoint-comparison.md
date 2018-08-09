---
title: En quoi le point de terminaison Azure AD v2.0 est-il différent ? | Microsoft Docs
description: Une comparaison entre la version Azure AD d’origine et les points de terminaison v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580252"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Quelles sont les particularités du point de terminaison v2.0 ?

Si vous connaissez bien le service Azure Active Directory (Azure AD) ou si vous avez déjà intégré des applications à Azure AD, certaines différences dans le point de terminaison v2.0 pourraient vous surprendre. Cet article détaille ces différences afin de clarifier les choses.

> [!NOTE]
> Les scénarios et les fonctionnalités d’Azure AD ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Comptes Microsoft et comptes Azure AD

Le point de terminaison v2.0 permet aux développeurs d’écrire des applications qui prennent en charge la connexion à partir de comptes Microsoft et de comptes Azure AD, à l’aide d’un seul point de terminaison d’authentification. Ces applications peuvent donc être parfaitement indépendantes du type de compte avec lequel l’utilisateur se connecte. Il est possible de faire en sorte qu’elles reconnaissent le type de compte utilisé dans une session particulière, mais ce n’est pas obligatoire.

Par exemple, si votre application appelle [Microsoft Graph](https://graph.microsoft.io), certaines fonctionnalités et données supplémentaires seront disponibles pour les utilisateurs de l’entreprise, telles que leurs sites SharePoint ou les données Directory. Néanmoins, pour de nombreuses actions, telles que la [lecture du courrier d’un utilisateur](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), le code peut être exactement le même pour les comptes Microsoft et Azure AD. 

L’intégration de votre application aux comptes Microsoft et Azure AD est désormais un simple processus. Vous pouvez utiliser un seul et même ensemble de points de terminaison, une seule et même bibliothèque et une seule et même inscription d’application pour accéder aux mondes des consommateurs et de l’entreprise. Pour en savoir plus sur le point de terminaison v2.0, consultez [la vue d’ensemble](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nouveau portail d’inscription des applications

Pour inscrire une application qui fonctionne avec le point de terminaison v2.0, vous devrez utiliser le portail d’inscription des applications Microsoft : [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Il s’agit du portail où vous pouvez obtenir un ID d’application, personnaliser l’apparence de la page de connexion de votre application, et bien plus encore. Pour accéder au portail, vous devez avoir un compte Microsoft alimenté : compte personnel ou professionnel/scolaire.

## <a name="one-app-id-for-all-platforms"></a>Un ID d’application pour toutes les plateformes

Si vous avez déjà utilisé Azure AD, vous avez probablement inscrit plusieurs applications différentes pour un seul et même projet. Par exemple, si vous avez créé un site web et une application iOS, vous avez dû les inscrire séparément, en utilisant deux ID d’application différents. Le portail d’inscription des applications Azure AD vous a obligé à faire cette distinction pendant l’inscription :

![Ancienne interface utilisateur de l’inscription des applications](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

De même, si vous aviez un site web et une API web principale, vous avez pu les inscrire chacun en tant qu’application distincte dans Azure AD. Si vous aviez une application iOS et une application Android, vous pouvez également avoir inscrit deux applications différentes. L’inscription des différents composants d’une application a entraîné des comportements inattendus pour les développeurs et leurs clients :

* Chaque composant apparaissait comme une application distincte dans le locataire (tenant) Azure AD de chaque client.
* Lorsqu’un administrateur client souhaitait appliquer des stratégies pour gérer l’accès à une application ou supprimer une application, il devait le faire pour chaque composant de l’application.
* Lorsque les clients donnaient leur consentement pour une application, chaque composant apparaissait dans l’écran de consentement comme une application distincte.

Avec le point de terminaison v2.0, vous pouvez maintenant inscrire tous les composants de votre projet comme une seule et même application, et utiliser un ID d’application unique pour l’ensemble du projet. Vous pouvez ajouter plusieurs « plateformes » pour chaque projet, et fournir les données appropriées pour chaque plateforme ajoutée. Bien entendu, vous pouvez créer autant d’applications que vous le souhaitez selon vos besoins, mais pour la plupart des cas, un seul ID d’application est nécessaire.

Notre objectif est de simplifier encore davantage la gestion des applications et l’expérience de développement, et de créer une vue plus consolidée d’un seul et même projet sur lequel vous travaillez peut-être.

## <a name="scopes-not-resources"></a>Des étendues, pas des ressources

Dans Azure AD, une application peut se comporter comme une **ressource** ou comme un destinataire de jetons. Une ressource peut définir plusieurs **étendues** ou **oAuth2Permissions** qu’elle comprend, permettant ainsi aux applications clientes de demander des jetons pour cette ressource pour un ensemble d’étendues donné. Prenez comme exemple de ressource l’API Graph d’Azure AD :

* Identificateur de ressource, ou `AppID URI` : `https://graph.windows.net/`
* Étendues, ou `OAuth2Permissions` : `Directory.Read`, `Directory.Write`, etc. 

Tout ceci est vrai pour le point de terminaison v2.0. Une application peut toujours se comporter comme une ressource, définir des étendues et être identifiée par un URI. Les applications clientes peuvent toujours demander l’accès à ces étendues. Toutefois, la manière dont le client demande ces autorisations a changé. Dans le passé, une demande d’autorisation OAuth 2.0 à Azure AD pouvait ressembler à ceci :

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

où le paramètre de **ressource** indiquait la ressource pour laquelle l’application cliente demandait une autorisation. Azure AD calculait les autorisations requises par l’application en fonction de la configuration statique définie sur le Portail Azure, puis émettait les jetons en conséquence. Désormais, la même demande d’autorisation OAuth 2.0 ressemble à ceci :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

où le paramètre d’ **étendue** indique la ressource et les autorisations pour lesquelles l’application demande une autorisation. La ressource souhaitée est toujours présente dans la demande ; elle est simplement englobée dans chacune des valeurs du paramètre d’étendue. L’utilisation du paramètre d’étendue de cette manière permet au point de terminaison v2.0 d’être plus conforme à la spécification OAuth 2.0, et elle est en harmonie plus étroite avec les pratiques courantes du secteur. Elle permet également aux applications d’effectuer un [consentement incrémentiel](#incremental-and-dynamic-consent), qui est décrit dans la section suivante.

## <a name="incremental-and-dynamic-consent"></a>Consentement incrémentiel et dynamique

Les applications inscrites dans Azure AD devaient auparavant spécifier leurs autorisations OAuth 2.0 requises sur le Portail Azure au moment de leur création :

![Interface utilisateur de l’inscription des autorisations](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

Les autorisations requises par une application étaient configurées **statiquement**. Si cela permettait à la configuration de l’application de se trouver sur le Portail Azure tout en préservant la simplicité du code, cela présentait quelques problèmes pour les développeurs :

* L’application devait connaître toutes les autorisations dont elle aurait besoin lors de sa création. L’ajout d’autorisations au fil du temps était un processus difficile.
* Une application devait connaître au préalable l’ensemble des ressources auxquelles elle aurait accès. Il était difficile de créer des applications pouvant accéder à un nombre arbitraire de ressources.
* Une application devait demander toutes les autorisations dont elle aurait besoin dès la première connexion de l’utilisateur. Dans certains cas, il en résultait une longue liste d’autorisations, qui dissuadait les utilisateurs finaux d’approuver l’accès de l’application à la connexion initiale.

Avec le point de terminaison v2.0, vous pouvez spécifier les autorisations dont votre application a besoin **dynamiquement**, lors de l’exécution, pendant l’utilisation régulière de votre application. Pour ce faire, vous pouvez spécifier les étendues nécessaires à votre application, à un moment donné, en les incluant dans le paramètre `scope` d’une demande d’autorisation :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Le paramètre ci-dessus demande l’autorisation pour l’application de lire les données d’annuaire d’un utilisateur d’Azure AD et d’écrire des données dans son annuaire. Si l’utilisateur a déjà accepté ces autorisations pour cette application, il entrera ses informations d’identification et se connectera à l’application. Si l’utilisateur n’a accepté aucune de ces autorisations, le point de terminaison v2.0 lui demande de corriger ce manquement. Pour en savoir plus, consultez les [autorisations, consentements et étendues](v2-permissions-and-consent.md).

En autorisant l’application à demander des autorisations dynamiquement grâce au paramètre `scope`, vous pourrez contrôler totalement l’expérience de vos utilisateurs. Si vous le souhaitez, vous pouvez choisir d’anticiper votre expérience de consentement et demander toutes les autorisations dans une demande d’autorisation initiale. Si votre application nécessite un grand nombre d’autorisations, vous pouvez choisir de rassembler celles de l’utilisateur de façon incrémentielle lorsqu’il essaie d’utiliser certaines fonctionnalités de votre application au fil du temps.

## <a name="well-known-scopes"></a>Étendues connues

### <a name="offline-access"></a>Accès hors connexion

Les applications qui utilisent le point de terminaison v2.0 peuvent nécessiter l’utilisation d’une nouvelle autorisation bien connue pour les applications : l’étendue `offline_access`. Toutes les applications doivent demander cette autorisation si elles doivent accéder aux ressources au nom d’un utilisateur pendant une période prolongée, même si l’utilisateur n’utilise peut-être pas activement l’application donnée. L’étendue `offline_access` apparaît à l’utilisateur dans la boîte de dialogue de consentement « Accéder aux données hors connexion », que l’utilisateur doit accepter. Demander l’autorisation `offline_access` permet à votre application web de recevoir les jetons d’actualisation OAuth 2.0 du point de terminaison v2.0. Les jetons d’actualisation sont de longue durée, et peuvent être échangés contre les nouveaux jetons d’accès OAuth 2.0 pour des périodes d’accès prolongées. 

Si votre application ne sollicite pas l’étendue `offline_access`, elle ne reçoit pas les jetons d’actualisation. Ainsi, lorsque vous échangez un code d’autorisation dans un flux de code d’autorisation OAuth 2.0, vous recevez uniquement un jeton d’accès du point de terminaison `/token`. Ce jeton d’accès demeure valide pendant une courte période (généralement une heure), avant d’arriver à expiration. À ce stade, votre application doit rediriger l’utilisateur vers le point de terminaison `/authorize` afin de récupérer un nouveau code d’autorisation. Pendant cette redirection, il peut être demandé à l’utilisateur d’entrer à nouveau ses informations d’identification ou d’accepter une nouvelle fois les autorisations, en fonction du type d’application.

Pour en savoir plus sur OAuth 2.0, les jetons d’actualisation et les jetons d’accès, consultez [Informations de référence sur les protocoles du point de terminaison v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil et e-mail

Historiquement, le flux de connexion OpenID Connect le plus simple avec Azure AD fournissait de nombreuses informations sur l’utilisateur dans l’élément id_token qui en résultait. Les revendications contenues dans un paramètre id_token peuvent inclure différentes informations utilisateur : son nom, son nom d’utilisateur par défaut, son adresse de messagerie, son ID objet, etc.

Les informations auxquelles l’étendue `openid` permet à votre application d’accéder sont maintenant restreintes. L’étendue `openid` ne l’autorisera qu’à connecter l’utilisateur et à recevoir un identificateur propre à l’application pour l’utilisateur. Si vous souhaitez obtenir des données personnelles sur l’utilisateur dans votre application, celle-ci devra lui demander des autorisations supplémentaires. Deux nouvelles étendues, `email` et `profile`, vous permettront de le faire.

L’étendue `email` permet à votre application d’accéder à l’adresse e-mail principale de l’utilisateur par le biais de la revendication `email` contenue dans id_token. 

L’étendue `profile` permet à votre application d’accéder à toutes les autres informations de base sur l’utilisateur (nom, nom d’utilisateur par défaut, ID objet, etc.).

Vous pouvez ainsi coder votre application en en divulguant le moins possible : vous pouvez vous contenter de demander à l’utilisateur les informations nécessaires à l’exécution de votre application. Pour plus d’informations sur ces étendues, voir [Référence sur les étendues v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Demandes de jetons

Les revendications des jetons émis par le point de terminaison v2.0 ne seront pas identiques aux jetons émis par les points de terminaison Azure AD mis à la disposition générale. Les applications qui passent au nouveau service ne doivent pas partir du principe que chaque revendication sera présente dans id_tokens ou access_tokens. Pour en savoir plus sur les demandes spécifiques émises dans les jetons v2.0, consultez [Informations de référence sur les jetons v2.0](v2-id-and-access-tokens.md).

## <a name="limitations"></a>Limites

Il existe quelques restrictions à connaître lors de l’utilisation du point v2.0. Pour savoir si ces restrictions s’appliquent à votre scénario, voir le [document relatif aux limites v2.0](active-directory-v2-limitations.md).
