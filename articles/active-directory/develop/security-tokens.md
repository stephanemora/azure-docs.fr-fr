---
title: Jetons de sécurité | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les principes de base des jetons de sécurité dans la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795642"
---
# <a name="security-tokens"></a>Jetons de sécurité

Un fournisseur d’identité centralisé est particulièrement utile pour les applications dont les utilisateurs se trouvant dans le monde entier ne se connectent pas nécessairement à partir du réseau de l’entreprise. La plateforme d’identités Microsoft authentifie les utilisateurs et fournit des jetons de sécurité, tels que des [jetons d’accès](developer-glossary.md#access-token), des [jetons d’actualisation](developer-glossary.md#refresh-token) et des [jetons d’ID](developer-glossary.md#id-token). Les jetons de sécurité permettent à une [application cliente](developer-glossary.md#client-application) d’accéder à des ressources protégées sur un [serveur de ressources](developer-glossary.md#resource-server).

**Jeton d’accès** : Un jeton d’accès est un jeton de sécurité émis par un [serveur d’autorisation](developer-glossary.md#authorization-server) dans le cadre d’un flux [OAuth 2.0](active-directory-v2-protocols.md). Il contient des informations sur l’utilisateur et la ressource à laquelle le jeton est destiné. Les informations peuvent être utilisées pour accéder à des API web et à d’autres ressources protégées. Les jetons d’accès sont validés par des ressources pour accorder l’accès à une application cliente. Pour en savoir plus sur la façon dont la plateforme d’identités Microsoft émet des jetons d’accès, consultez [Jetons d’accès](access-tokens.md).

**Jeton d’actualisation** : Les jetons d’accès n’étant valides que pendant une courte période, les serveurs d’autorisation émettent parfois un jeton d’actualisation en même temps que le jeton d’accès. L’application cliente peut alors échanger ce jeton d’actualisation contre un nouveau jeton d’accès si nécessaire. Pour en savoir plus sur la façon dont la plateforme d’identités Microsoft utilise des jetons d’actualisation pour révoquer des autorisations, consultez [Révocation des jetons](access-tokens.md#token-revocation).

**Jeton d’ID** : Les jetons d’ID sont envoyés à l’application cliente dans le cadre d’un flux [OpenID Connect](v2-protocols-oidc.md). Ils peuvent être envoyés en même temps qu’un jeton d’accès ou à la place de celui-ci. Les jetons d’ID sont utilisés par le client pour authentifier l’utilisateur. Pour en savoir plus sur la façon dont la plateforme d’identités Microsoft émet des jetons d’ID, consultez [Jetons d’ID](id-tokens.md).

> [!NOTE]
> Cet article traite des jetons de sécurité utilisés par les protocoles OAuth2 et OpenID Connect. De nombreuses applications d’entreprise utilisent SAML pour authentifier les utilisateurs. Pour plus d’informations sur les assertions SAML, consultez [Référence de jeton SAML Azure Active Directory](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Valider les jetons de sécurité

La validation du jeton revient à l’application pour laquelle le jeton a été généré, à l’application web qui a connecté l’utilisateur ou à l’API web appelée. Le jeton est signé par le serveur d’autorisation avec une clé privée. Le serveur d’autorisation publie la clé publique correspondante. Pour valider un jeton, l’application vérifie la signature à l’aide de la clé publique du serveur d’autorisation et confirme ainsi que la signature a été créée au moyen de la clé privée.

Les jetons ne sont valides que pour une durée limitée. En règle générale, le serveur d’autorisation fournit une paire de jetons, par exemple :

* Un jeton d’accès, qui accède à l’application ou à la ressource protégée.
* Un jeton d’actualisation, utilisé pour actualiser le jeton d’accès lorsque celui-ci est sur le point d’expirer.

Les jetons d’accès sont passés à une API web en tant que jeton du porteur dans l’en-tête `Authorization`. Une application peut fournir un jeton d’actualisation au serveur d’autorisation. Si l’accès de l’utilisateur à l’application n’a pas été révoqué, un nouveau jeton d’accès et un nouveau jeton d’actualisation sont obtenus en retour. C’est de cette façon que le scénario d’une personne quittant l’entreprise est géré. Lorsque le serveur d’autorisation reçoit le jeton d’actualisation, il n’émet pas d’autre jeton d’accès valide si l’utilisateur n’est plus autorisé.

## <a name="json-web-tokens-and-claims"></a>Jetons JSON Web Token et revendications

La plateforme d’identités Microsoft implémente des jetons de sécurité comme des jetons JSON Web Token (JWT) qui contiennent des *revendications*. Étant donné que les jetons JWT sont utilisés comme jetons de sécurité, cette forme d’authentification est parfois appelée *Authentification JWT*.

Une [revendication](developer-glossary.md#claim) fournit des assertions sur une entité (telle qu’une application cliente ou un [propriétaire de ressources](developer-glossary.md#resource-owner)) à une autre entité, telle qu’un serveur de ressources. Une revendication peut également être appelée revendication JWT ou revendication JSON Web Token.

Les revendications sont des paires de noms ou de valeurs qui relaient des faits sur le sujet du jeton. Par exemple, une revendication peut contenir des faits sur le principal de sécurité authentifié par le serveur d’autorisation. Les revendications présentes dans un jeton spécifique dépendent de plusieurs variables, comme le type de jeton, le type d’informations d’identification utilisées pour authentifier le sujet et la configuration de l’application.

Les applications peuvent utiliser des revendications pour diverses tâches, par exemple :

* Valider le jeton.
* Identifier le [locataire](developer-glossary.md#tenant) du sujet du jeton.
* Afficher les informations utilisateur.
* Déterminer l’autorisation du sujet.

Une revendication se compose de paires clé-valeur qui communiquent des informations, telles que les :

* serveur de jetons de sécurité qui a généré le jeton ;
* date à laquelle le jeton a été généré ;
* sujet (tel que l’utilisateur, à l’exception des démons) ;
* audience, qui est l’application pour laquelle le jeton a été généré ;
* application (le client) qui a demandé le jeton. Dans le cas d’applications web, il peut s’agir de l’audience.

Pour en savoir plus sur la façon dont la plateforme d’identités Microsoft implémente des jetons et les informations de revendication, consultez [Jetons d’accès](access-tokens.md) et [Jetons d’ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Comment chaque flux émet des jetons et des codes

Selon la façon dont votre client est créé, il peut utiliser un ou plusieurs des flux d’authentification pris en charge par la plateforme d’identité Microsoft. Ces flux peuvent produire différents jetons (jetons d’ID, jetons d’actualisation, jetons d’accès) et des codes d’autorisation. Ils nécessitent différents jetons pour fonctionner. Ce tableau fournit une vue d’ensemble.

|Flux | Nécessite | Jeton d’ID | Access token (Jeton d’accès) | Jeton d’actualisation | Code d’autorisation. |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flux du code d’autorisation](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Flux implicite](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Circuit OIDC hybride](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Échange de jetons d’actualisation](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Jeton d’actualisation | x | x | x| |
|[Flux On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | Access token (Jeton d’accès)| x| x| x| |
|[Informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) | | | x (application uniquement)| | |

Les jetons émis via le mode implicite ont une longueur maximale, car ils sont renvoyés au navigateur via l’URL, où `response_mode` est `query` ou `fragment`. Certains navigateurs limitent la taille de l’URL qui peut être placée dans la barre d’adresse et refusent les URL trop longues. Par conséquent, ces jetons n’ont pas de revendications `groups` ou `wids`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’authentification et l’autorisation dans la plateforme d’identités Microsoft, consultez les articles suivants :

* Pour en savoir plus sur les concepts de base de l’authentification et de l’autorisation, consultez [Authentification ou autorisation](authentication-vs-authorization.md).
* Pour en savoir plus sur l’inscription de votre application pour l’intégration, consultez [Modèle d’application](application-model.md).
* Pour en savoir plus sur le flux de connexion des applications web, de bureau et mobiles, consultez [Flux de connexion des applications](app-sign-in-flow.md).
