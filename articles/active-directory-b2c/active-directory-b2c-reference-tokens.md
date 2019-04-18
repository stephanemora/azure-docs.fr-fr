---
title: Vue d’ensemble de jetons - Azure Active Directory B2C | Microsoft Docs
description: En savoir plus sur les jetons utilisés dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680262"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Vue d’ensemble de jetons dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure B2C Active de Directory (Azure AD) B2C émet plusieurs types de jetons de sécurité lors du traitement de chaque [flux d’authentification](active-directory-b2c-apps.md). Ce document décrit le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

## <a name="token-types"></a>Types de jetons

Azure AD B2C prend en charge la [protocoles OAuth 2.0 et OpenID Connect](active-directory-b2c-reference-protocols.md), qui utilise des jetons pour l’authentification et l’accès sécurisé aux ressources. Tous les jetons utilisés dans Azure AD B2C sont [jetons web JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) qui contiennent des assertions d’informations concernant le porteur et le sujet du jeton.

Les jetons suivants sont utilisés dans la communication avec Azure AD B2C :

- *Jeton d’ID* -JWT A contenant des revendications que vous pouvez utiliser pour identifier les utilisateurs dans votre application. Ce jeton est envoyé en toute sécurité dans les requêtes HTTP pour la communication entre deux composants de la même application ou service. Vous pouvez utiliser les revendications dans un jeton d’ID comme vous le souhaitez. Ils sont souvent utilisés pour afficher les informations de compte ou pour prendre des décisions de contrôle d’accès dans une application. Jetons d’ID sont signés, mais ils ne sont pas chiffrés. Lorsque votre application ou votre API reçoit un jeton d’ID, elle doit valider la signature pour prouver que le jeton est authentique. Votre application ou votre API doit également valider certaines revendications du jeton pour prouver qu’il est valide. Selon les exigences de scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer des validations de revendication communes dans chaque scénario.
- *Jeton d’accès* -JWT A contenant des revendications que vous pouvez utiliser pour identifier les autorisations accordées à votre API. Jetons d’accès sont signés, mais ils ne sont pas chiffrés. Jetons d’accès sont utilisés pour fournir l’accès aux API et serveurs de ressources.  Lorsque votre API reçoit un jeton d’accès, il doit valider la signature pour prouver que le jeton est authentique. Votre API doit également valider certaines revendications du jeton pour prouver qu’il est valide. Selon les exigences de scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer des validations de revendication communes dans chaque scénario.
- *Jeton d’actualisation* -jetons d’actualisation servent à acquérir de nouveaux jetons d’ID et d’accès dans un flux OAuth 2.0. Ils fournissent à votre application avec un accès à long terme à des ressources pour le compte utilisateurs sans nécessiter l’intervention de ces utilisateurs. Jetons d’actualisation sont opaques pour votre application. Ils sont émis par Azure AD B2C et peuvent être inspectés et interprétés que par Azure AD B2C. Ils sont de longue durées, mais votre application ne doit pas être écrite dans l’attente un jeton d’actualisation est valable pendant une période spécifique. Les jetons d’actualisation peuvent être rendus non valides à tout moment, et ce pour diverses raisons. Il est le seul moyen pour votre application savoir si un jeton d’actualisation est valide pour tenter de l’utiliser en effectuant une demande de jeton à Azure AD B2C. Lorsque vous échangez un jeton d’actualisation pour un nouveau jeton, vous recevez un nouveau jeton d’actualisation dans la réponse de jeton. Enregistrer le nouveau jeton d’actualisation. Il remplace le jeton d’actualisation que vous avez utilisé précédemment dans la demande. Cette action permet de garantir que vos jetons d’actualisation resteront valides aussi longtemps que possible. 

## <a name="endpoints"></a>Points de terminaison

Un [inscrit application](tutorial-register-applications.md) reçoit les jetons et communique avec Azure AD B2C en envoyant des demandes à ces points de terminaison :

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Les jetons de sécurité reçu par votre application à partir d’Azure AD B2C peuvent provenir de la `/authorize` ou `/token` points de terminaison. Lorsque les jetons d’ID sont acquis à partir de la `/authorize` point de terminaison, elles sont effectuées à l’aide de la [flux implicite](active-directory-b2c-reference-spa.md), qui est souvent utilisée pour les utilisateurs se connectant aux applications web basées sur javascript. Lorsque les jetons d’ID sont acquis à partir du point de terminaison `/token`, ils utilisent le [flux de code confidentiel](active-directory-b2c-reference-oidc.md), qui cache le jeton au navigateur.

## <a name="claims"></a>Revendications

Lorsque vous utilisez Azure AD B2C, vous disposez d’un contrôle affiné du contenu de vos jetons. Vous pouvez configurer [flux d’utilisateurs](active-directory-b2c-reference-policies.md) et [stratégies personnalisées](active-directory-b2c-overview-custom.md) pour envoyer certains ensembles de données utilisateur dans les revendications qui sont obligatoires pour votre application. Ces revendications peuvent inclure des propriétés standard telles que **displayName** et **emailAddress**. Vos applications peuvent utiliser ces revendications pour authentifier de manière sécurisée les utilisateurs et les demandes. 

Les revendications dans les jetons d’ID ne sont pas retournées dans un ordre particulier. Nouvelles revendications peuvent être introduites dans les jetons d’ID à tout moment. Votre application ne doit pas perturber comme nouvelles revendications sont ajoutées. Vous pouvez également inclure [des attributs utilisateur personnalisés](active-directory-b2c-reference-custom-attr.md) dans vos revendications.

Le tableau suivant répertorie les revendications que vous pouvez attendre dans les jetons d’ID et jetons émis par Azure AD B2C d’accès.

| Nom | Revendication | Exemple de valeur | Description |
| ---- | ----- | ------------- | ----------- |
| Audience | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifie le destinataire du jeton. Dans Azure AD B2C, l’audience est l’ID d’application. Votre application doit valider cette valeur et rejeter le jeton s’il ne correspond pas. Audience est synonyme de ressource. |
| Émetteur | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton. Il identifie également le répertoire dans lequel l’utilisateur a été authentifié. Votre application doit valider la revendication de l’émetteur pour vous assurer que le jeton provient d’un point de terminaison approprié. |
| Émis à | `iat` | `1438535543` | Heure à laquelle le jeton a été émis, représentée en heure epoch. |
| Heure d’expiration | `exp` | `1438539443` | Heure à laquelle le jeton n’est plus valide, représentée en heure epoch. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie de jeton. |
| Pas avant | `nbf` | `1438535543` | Heure à laquelle le jeton est valide, représentée en heure epoch. Cette durée est généralement identique à la fois que le jeton a été émis. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie de jeton. |
| Version | `ver` | `1.0` | La version du jeton d’ID, tel que défini par Azure AD B2C. |
| Hachage de code | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hachage de code inclus dans un jeton d’ID que si le jeton est émis avec un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d’informations sur la façon d’effectuer cette validation, consultez le [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hachage de jeton d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hachage de jeton d’accès inclus dans un jeton d’ID que si le jeton est émis avec un jeton d’accès OAuth 2.0. Un hachage de jeton d’accès peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d’informations sur la façon d’effectuer cette validation, consultez le [spécification OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Valeur à usage unique | `nonce` | `12345` | La valeur à usage unique est une stratégie d’atténuation des attaques par relecture de jetons. Votre application peut spécifier une valeur à usage unique dans une demande d’autorisation à l’aide de le `nonce` paramètre de requête. La valeur que vous fournissez dans la demande est émise sans aucune modification dans le `nonce` d’un jeton d’ID de revendication. Cette revendication permet à votre application vérifier la valeur par rapport à la valeur spécifiée dans la demande. Votre application doit effectuer cette validation pendant le processus de validation du jeton d’ID. |
| Objet | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Le principal sur lequel le jeton déclare les informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. Vous pouvez l’utiliser pour effectuer des vérifications d’autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Par défaut, la revendication de l’objet est remplie avec l’ID d’objet de l’utilisateur dans le répertoire. |
| Référence de classe du contexte d’authentification | `acr` | Non applicable | Utilisé uniquement avec des stratégies plus anciennes. |
| Stratégie d’infrastructure de confiance | `tfp` | `b2c_1_signupsignin1` | Le nom de la stratégie qui a été utilisée pour obtenir le jeton d’ID. |
| Moment de l’authentification | `auth_time` | `1438535543` | Heure à laquelle un utilisateur dernières informations d’identification entrées, représentée en heure epoch. |
| Étendue | `scp` | `Read`| Les autorisations accordées à la ressource pour un jeton d’accès. Plusieurs autorisations octroyées sont séparées par un espace. |
| Tiers autorisé | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **ID** de l’application cliente qui a initié la demande. |

## <a name="configuration"></a>Configuration

Les propriétés suivantes sont utilisées pour [gérer les durées de vie des jetons de sécurité](configure-tokens.md) émis par Azure AD B2C :

- **Durées de vie du jeton d’accès et d’ID (minutes)** : la durée de vie du jeton du porteur OAuth 2.0 utilisé pour accéder à une ressource protégée. La valeur par défaut est 60 minutes. La valeur minimale (inclusive) est de 5 minutes. La valeur maximale (inclusive) est de 1 440 minutes.

- **Durée de vie de jeton d’actualisation (jours)** : la période maximale avant laquelle un jeton d’actualisation peut être utilisé pour acquérir un nouveau jeton d’accès ou ID. La période de temps couvre également l’acquisition d’un nouveau jeton d’actualisation si votre application a reçu le `offline_access` étendue. La valeur par défaut est de 14 jours. La valeur minimale (inclusive) est un jour. La valeur maximale (inclusive) est de 90 jours.

- **Actualiser le jeton durée de vie fenêtre glissante (jours)** : après cette période de temps s’écoule à l’utilisateur est obligé de s’authentifier de nouveau, quelle que soit la période de validité du dernier jeton d’actualisation obtenu par l’application. Cette valeur ne peut être fournie que si le commutateur est défini sur **Délimité**. Elle doit être supérieure ou égale à la valeur **Durée de vie du jeton d’actualisation (jours)** . Si le commutateur est défini sur **Non délimité**, vous ne pouvez pas fournir une valeur spécifique. La valeur par défaut est de 90 jours. La valeur minimale (inclusive) est un jour. La valeur maximale (inclusive) est de 365 jours.

Les cas d’usage suivants sont activés à l’aide de ces propriétés :

- Autoriser un utilisateur à rester connecté à une application mobile indéfiniment, tant qu’il est actif en permanence sur l’application. Vous pouvez définir le paramètre **Durée de vie fenêtre glissante du jeton d’actualisation (jours)** sur **Non délimité** dans votre flux d’utilisateur de connexion.
- Respectez les exigences de conformité et de sécurité de votre secteur en définissant les durées de vie correctes du jeton d’accès.

Ces paramètres ne sont pas disponibles pour les flux d’utilisateur de réinitialisation de mot de passe. 

## <a name="compatibility"></a>Compatibilité

Les propriétés suivantes sont utilisées pour [gérer la compatibilité de jeton](configure-tokens.md):

- **Revendication de l’émetteur (iss)**  : cette propriété identifie le client Azure AD B2C qui a émis le jeton. La valeur par défaut est `https://<domain>/{B2C tenant GUID}/v2.0/`. La valeur de `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclut l’ID pour le locataire Azure AD B2C et le flux de l’utilisateur qui a été utilisé dans la demande de jeton. Si votre application ou bibliothèque a besoin d’Azure AD B2C soit conforme à la [OpenID Connect Discovery 1.0 spec](https://openid.net/specs/openid-connect-discovery-1_0.html), utilisez cette valeur.

- **Revendication d’objet (obj)**  : cette propriété identifie l’entité pour laquelle le jeton indique des informations. La valeur par défaut est **ObjectID**, qui remplit le `sub` de revendication dans le jeton avec l’ID d’objet de l’utilisateur. La valeur de **ne pas pris en charge** est fourni uniquement pour la compatibilité descendante. Il est recommandé que vous basculez vers **ObjectID** dès que vous êtes en mesure de.

- **ID de stratégie représentant la revendication** -cette propriété identifie le type de revendication dans laquelle le nom de stratégie utilisé dans la demande de jeton est rempli. La valeur par défaut est `tfp`. La valeur de `acr` est fourni uniquement pour la compatibilité descendante.

## <a name="pass-through"></a>Requête directe

Quand un parcours utilisateur démarre, Azure AD B2C reçoit un jeton d’accès à partir d’un fournisseur d’identité. Azure AD B2C utilise ce jeton pour récupérer des informations sur l’utilisateur. Vous [activer une revendication dans votre flux utilisateur](idp-pass-through-user-flow.md) ou [définir une revendication dans votre stratégie personnalisée](idp-pass-through-custom.md) pour transmettre le jeton via aux applications que vous inscrivez dans Azure AD B2C. Votre application doit être à l’aide un [un flux utilisateur v2](user-flow-versions.md) en tirer parti en passant le jeton en tant que revendication.

Azure AD B2C prend uniquement en passant le jeton d’accès OAuth 2.0 de fournisseurs d’identité, notamment Facebook et Google. Pour tous les autres fournisseurs d’identité, la revendication est retournée vide. 

## <a name="validation"></a>Validation

Pour valider un jeton, votre application doit vérifier la signature et les revendications du jeton. De nombreuses bibliothèques open source sont disponibles pour valider les jetons Jwt, selon votre langage préféré. Il est recommandé que vous explorez ces options au lieu d’implémentez votre propre logique de validation.

### <a name="validate-signature"></a>Valider la signature

Un jeton JWT contient trois segments, un *en-tête*, un *corps*et un *signature*. Le segment de signature peut être utilisé pour valider l’authenticité du jeton afin qu’il soit approuvé par votre application. Les jetons Azure AD B2C sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. 

L’en-tête du jeton contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La valeur de la **alg** revendication est l’algorithme qui a été utilisé pour signer le jeton. La valeur de la **kid** revendication est la clé publique qui a été utilisée pour signer le jeton. À un moment donné, Azure AD B2C peut signer un jeton à l’aide de l’un d’un ensemble de paires de clés publique / privée. Azure AD B2C fait pivoter le jeu de clés possible régulièrement. Votre application doit être écrite pour gérer automatiquement ces changements de clés. Pour vérifier les mises à jour des clés publiques utilisées par Azure AD B2C, une fréquence raisonnable est de 24 heures.

Azure AD B2C a un point de terminaison des métadonnées OpenID Connect. À l’aide de ce point de terminaison, les applications peuvent demander des informations sur Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Votre client Azure AD B2C contient un document de métadonnées JSON pour chaque stratégie. Le document de métadonnées est un objet JSON qui contient plusieurs informations utiles. Contient les métadonnées **jwks_uri**, qui indique l’emplacement de l’ensemble des clés publiques qui sont utilisés pour signer les jetons. Qu’emplacement est fourni ici, mais il de préférable d’extraire l’emplacement dynamiquement en utilisant le document de métadonnées et de l’analyse **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Le document JSON situé à cette URL contient toutes les informations sur les clés publiques utilisées à un moment donné. Votre application peut utiliser les revendications `kid` dans l’en-tête JWT pour sélectionner la clé publique utilisée dans le document JSON pour signer un jeton donné. Elle peut ensuite procéder à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

Le document de métadonnées pour le `B2C_1_signupsignin1` stratégie dans le `contoso.onmicrosoft.com` locataire se trouve dans :

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Pour déterminer quelle stratégie a été utilisée pour signer un jeton (et où aller pour demander les métadonnées), vous avez deux options. Tout d’abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton. Vous pouvez analyser les revendications contenues dans le corps du jeton JWT par le biais d’un décodage base-64 du corps et la désérialisation de la chaîne JSON résultante. Le `acr` revendication est le nom de la stratégie qui a été utilisé pour émettre le jeton. L’autre option consiste à coder la stratégie dans la valeur de la `state` paramètre lorsque vous émettez la requête et puis le décoder pour déterminer quelle stratégie a été utilisée. Les 2 méthodes sont valides.

Ce document ne contient pas la description de la procédure de validation de la signature. De nombreuses bibliothèques open source sont disponibles pour vous aider à valider un jeton.

### <a name="validate-claims"></a>Valider des revendications

Lorsque vos applications ou les API reçoit un jeton d’ID, elle doit également effectuer plusieurs vérifications sur les revendications dans le jeton d’ID. Les revendications suivantes doivent être vérifiées :

- **audience** -vérifie que le jeton d’ID était bien destiné à donner à votre application.
- **pas avant** et **délai d’expiration** -vérifie que le jeton d’ID n’a pas expiré.
- **émetteur** -vérifie que le jeton a été émis à votre application par Azure AD B2C.
- **valeur à usage unique** -une stratégie pour atténuer les attaques par relecture de jetons.

Pour obtenir une liste complète de votre application doit valider, reportez-vous à la [spécification OpenID Connect](https://openid.net).  

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [l’utilisation des jetons d’accès](active-directory-b2c-access-tokens.md).

