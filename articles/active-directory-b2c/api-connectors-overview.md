---
title: À propos des connecteurs d’API dans Azure AD B2C
description: Utilisez les connecteurs d’API Azure Active Directory (Azure AD) pour personnaliser et étendre vos flux d’utilisateurs à l’aide d’API REST.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 04/27/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a9eeb7ed664f67e1273a7dfff701a18970cd91fd
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108174521"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Utiliser des connecteurs d’API pour personnaliser et étendre les flux d’utilisateurs d’inscription

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

> [!IMPORTANT]
> Les connecteurs d’API pour l’inscription sont une fonctionnalité en préversion publique d’Azure AD B2C. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Vue d’ensemble 

En tant que développeur ou administrateur informatique, vous pouvez utiliser des connecteurs d’API pour intégrer vos flux d’utilisateurs d’inscription à des API REST pour personnaliser l’inscription et l’intégrer à des systèmes externes. Par exemple, avec les connecteurs d’API, vous pouvez :

- **Valider des données d’entrée utilisateur**. Validez par rapport à des données utilisateur incorrectes ou non valides. Par exemple, vous pouvez valider les données fournies par l’utilisateur par rapport à des données existantes dans une banque de données externe ou une liste de valeurs autorisées. En cas d’invalidité, vous pouvez demander à un utilisateur de fournir des données valides ou empêcher l’utilisateur de continuer le processus d’inscription.
- **Intégrer à un flux de travail d’approbation personnalisé**. Connectez-vous à un système d’approbation personnalisé pour gérer et limiter la création de comptes.
- **Remplacer des attributs utilisateur**. Reformatez ou attribuez une valeur à un attribut collecté auprès de l’utilisateur. Par exemple, si un utilisateur entre le prénom entier en lettres minuscules ou majuscules, vous pouvez modifier sa mise en forme en utilisant une majuscule uniquement pour la première lettre. 
- **Vérifier l’identité de l’utilisateur**. Utilisez un service de vérification d’identité pour ajouter un niveau supplémentaire de sécurité aux décisions de création de comptes.
- **Exécuter une logique métier personnalisée**. Vous pouvez déclencher des événements en aval dans vos systèmes ce cloud pour envoyer des notifications Push, mettre à jour des bases de données d’entreprise, gérer des autorisations, auditer des bases de données et effectuer d’autres actions personnalisées.

Un connecteur d’API fournit à Azure AD B2C les informations nécessaires pour appeler le point de terminaison d’API en définissant l’URL de point de terminaison HTTP et l’authentification pour l’appel d’API. Une fois que vous avez configuré un connecteur d’API, vous pouvez l’activer pour une étape spécifique dans un flux utilisateur. Quand un utilisateur atteint cette étape dans le flux d’inscription, le connecteur d’API est appelé et se matérialise en tant que requête HTTP POST à votre API, en envoyant des informations utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. La réponse d’API peut affecter l’exécution du flux utilisateur. Par exemple, la réponse d’API peut empêcher un utilisateur de s’inscrire, demander à l’utilisateur d’entre à nouveau des informations ou écraser et ajouter les attributs de l’utilisateur.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Où activer un connecteur d’API dans un flux utilisateur

Il existe deux emplacements dans un flux utilisateur où vous pouvez activer un connecteur d’API :

- après la connexion avec un fournisseur d’identité
- avant la création de l’utilisateur

> [!IMPORTANT]
> Dans ces deux cas, les connecteurs d’API sont appelés lors de l’**inscription** de l’utilisateur et non de la connexion.

### <a name="after-signing-in-with-an-identity-provider"></a>après la connexion avec un fournisseur d’identité

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local. Voici quelques exemples de scénarios de connecteur d’API que vous pouvez activer à cette étape :

- Utilisez l’adresse e-mail ou l’identité fédérée que l’utilisateur a fournie pour rechercher des revendications dans un système existant. Retournez ces revendications à partir du système existant, complétez la page de collection d’attributs, puis rendez-les disponibles pour retourner dans le jeton.
- Implémentez une liste d’autorisation ou de refus en fonction de l’identité sociale.

### <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé. Voici quelques exemples de scénarios que vous pouvez activer à cette étape au cours de l’inscription :

- Validez les données d’entrée utilisateur et demandez à un utilisateur de renvoyer des données.
- Bloquer l’inscription utilisateur en fonction des données entrées par l’utilisateur.
- Vérifier l’identité de l’utilisateur.
- Interrogez des systèmes externes pour obtenir des données existantes sur l’utilisateur afin de les retourner dans le jeton d’application ou de les stocker dans Azure AD.

::: zone-end

::: zone pivot="b2c-custom-policy"

La plateforme Identity Experience Framework sur laquelle repose Azure Active Directory B2C (Azure AD B2C) peut s’intégrer avec des API RESTful à l’intérieur d’un parcours utilisateur. Cet article explique comment créer un parcours utilisateur interagissant avec un service RESTful à l’aide d’un [profil technique RESTful](restful-technical-profile.md).

Azure AD B2C vous permet d’ajouter votre propre logique métier à un parcours utilisateur en appelant votre propre service RESTful. La plateforme Identity Experience Framework peut envoyer et recevoir des données de votre service RESTful pour échanger des revendications. Vous pouvez par exemple :

- **Valider des données d’entrée utilisateur**. Par exemple, vous pouvez vérifier que l’adresse e-mail fournie par l’utilisateur existe dans la base de données de votre client et, si ce n’est pas le cas, présenter une erreur.
- **Traiter des revendications**. Si un utilisateur entre son prénom entièrement en minuscules ou majuscules, votre API REST peut modifier la mise en forme en utilisant une majuscule uniquement pour la première lettre avant de le renvoyer à Azure AD B2C.
- **Enrichir les données utilisateur en les intégrant davantage avec des applications métier d’entreprise**. votre service RESTful peut recevoir l’adresse e-mail de l’utilisateur, interroger la base de données de clients et retourner le numéro de fidélité de l’utilisateur à Azure AD B2C. Les revendications retournées peuvent alors être stockées dans le compte Azure AD de l’utilisateur, évaluées dans les étapes d’orchestration suivantes ou incluses dans le jeton d’accès.
- **Exécuter une logique métier personnalisée**. Vous pouvez envoyer des notifications Push, mettre à jour des bases de données d’entreprise, exécuter un processus de migration utilisateur, gérer les autorisations, auditer des bases de données et effectuer d’autres flux de travail.

![Diagramme d’un échange de revendications de service RESTful](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> S’il n’y a pas de réponse ou que la réponse est lente depuis le service RESTful vers Azure AD B2C, le délai d’expiration est de 30 secondes et le nombre de tentatives est de 2 (ce qui signifie qu’il y a 3 tentatives au total). Les paramètres de délai d’expiration et de nombre de tentatives ne sont pas configurables actuellement.

## <a name="calling-a-restful-service"></a>Appel d’un service RESTful

Cette interaction inclut un échange de revendications d’informations entre les revendications de l’API REST et Azure AD B2C. Vous pouvez concevoir l’intégration aux services RESTful comme suit :

- **Profil technique de validation**. L’appel au service RESTful se produit à l’intérieur d’un [profil technique de validation](validation-technical-profile.md) du [profil technique autodéclaré](self-asserted-technical-profile.md) spécifié, ou d’un [contrôle d’affichage de vérification](display-control-verification.md) d’un [contrôle d’affichage](display-controls.md). Le profil technique de validation valide les données fournies par l’utilisateur avant la suite du parcours utilisateur. Avec le profil technique de validation, vous pouvez :

  - Envoyer des revendications à votre API REST.
  - Valider les revendications et lever des messages d’erreur personnalisés qui s’affichent pour l’utilisateur.
  - Renvoyer les revendications de l’API REST aux étapes suivantes de l’orchestration.

- **Échange de revendications**. Vous pouvez configurer un échange direct de revendications en appelant un profil technique d’API REST directement à partir d’une étape d’orchestration d’un [parcours utilisateur](userjourneys.md). Cette définition est limitée à :

  - Envoyer des revendications à votre API REST.
  - Valider les revendications et lever des messages d’erreur personnalisés qui sont retournés à l’application.
  - Renvoyer les revendications de l’API REST aux étapes suivantes de l’orchestration.

Vous pouvez ajouter un appel d’API REST à n’importe quelle étape du parcours utilisateur défini par une stratégie personnalisée. Par exemple, vous pouvez appeler une API REST :

- lors de la connexion, juste qu’Azure AD B2C valide les informations d’identification ;
- immédiatement après la connexion ;
- avant qu’Azure AD B2C crée un compte dans l’annuaire ;
- après qu’Azure AD B2C a créé un compte dans l’annuaire ;
- avant qu’Azure AD B2C émette un jeton d’accès.

![Collecte de profil technique de validation](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>Envoi de données

Dans le [profil technique RESTful](restful-technical-profile.md), l’élément `InputClaims` contient une liste de revendications à envoyer à votre service RESTful. Vous pouvez mapper le nom de votre revendication au nom défini dans le service RESTful, définir une valeur par défaut et utiliser des [programmes de résolution de revendications](claim-resolver-overview.md).

Vous pouvez configurer la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful en utilisant l’attribut SendClaimsIn. Les valeurs possibles sont les suivantes :

- **Body**, envoyé dans le corps de la requête HTTP POST au format JSON.
- **Form**, envoyé dans le corps de la requête HTTP POST au format de valeur de clé séparée par des perluètes (« & »).
- **Header**, envoyé dans l’en-tête de requête HTTP GET.
- **QueryString**, envoyé dans la chaîne de requête HTTP GET.

Quand l’option **Body** est configurée, le profil technique de l’API REST vous permet d’envoyer une charge utile JSON complexe à un point de terminaison. Pour plus d’informations, consultez [Envoyer une charge utile JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Réception de données

L’élément `OutputClaims` du [profil technique RESTful](restful-technical-profile.md) contient une liste de revendications retournées par l’API REST. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans l’API REST. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité de l’API REST, pour autant que vous définissiez l’attribut DefaultValue.

Les revendications de sortie analysées par le fournisseur de revendications RESTful s’attendent toujours à analyser une réponse de corps JSON plate, par exemple :

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Les revendications de sortie doivent ressembler à l’extrait xml suivant :

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Pour analyser une réponse de corps JSON imbriquée, définissez les métadonnées ResolveJsonPathsInJsonTokens sur true. Dans la revendication de sortie, définissez PartnerClaimType sur l’élément de chemin d’accès JSON que vous souhaitez générer.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Les revendications de sortie doivent ressembler à l’extrait xml suivant :

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>Localisez l’API REST

Dans un profil technique RESTful, vous pouvez envoyer la langue/les paramètres régionaux de la session active et, si nécessaire, déclencher un message d’erreur localisé. Le [programme de résolution des revendications](claim-resolver-overview.md) vous permet d’envoyer une revendication contextuelle, telle que la langue de l’utilisateur. L’exemple suivant présente un profil technique RESTful illustrant ce scénario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Gestion des messages d’erreur

Il se peut que votre API REST doive retourner un message d’erreur tel que « Utilisateur introuvable dans le système CRM ». Quand une erreur se produit, l’API REST doit retourner un message d’erreur HTTP 409 (code d’état de réponse Conflit). Pour plus d’informations, consultez le [Profil technique RESTful ](restful-technical-profile.md#returning-validation-error-message).

Ce comportement ne peut être obtenu qu’en appelant un profil technique d’API REST à partir d’un profil technique de validation. Cela permet à l’utilisateur de corriger les données sur la page et de réexécuter la validation lors de l’envoi de la page.

Si vous référencez un profil technique d’API REST directement à partir d’un parcours utilisateur, l’utilisateur est redirigé vers l’application basée sur les revendications avec le message d’erreur approprié.

::: zone-end

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Protégez le point de terminaison de votre API REST afin que seuls des clients authentifiés puissent communiquer avec celui-ci. L’API REST doit utiliser un point de terminaison HTTPS. Définissez le type d’authentification sur l’une des méthodes d’authentification suivantes.

### <a name="api-key"></a>Clé d’API

La clé API est un identificateur unique utilisé pour authentifier un utilisateur afin d’accéder à un point de terminaison d’API REST. Par exemple, le [déclencheur HTTP d’Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) inclut le `code` comme paramètre de requête dans l’URL du point de terminaison.

::: zone pivot="b2c-user-flow"

```http
https://contoso.azurewebsites.net/api/endpoint?code=0123456789 
```

L’authentification par clé API ne doit pas être utilisée seule en production. Par conséquent, la configuration de l'authentification de base ou par certificat est toujours requise. Si vous ne souhaitez implémenter aucune méthode d'authentification (non recommandé) à des fins de développement, vous pouvez choisir l'authentification de base et utiliser des valeurs temporaires pour `username` et `password`, que votre API peut ignorer pendant l'implémentation de l'autorisation dans votre API.

::: zone-end

::: zone pivot="b2c-custom-policy"

Une clé API peut être envoyée à un en-tête HTTP personnalisé. Par exemple, le [déclencheur HTTP Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) utilise l’en-tête HTTP `x-functions-key` pour identifier le demandeur.  

::: zone-end

### <a name="client-certificate"></a>Certificat client

L’authentification par certificat client est une méthode d’authentification réciproque basée sur un certificat, dans laquelle le client fournit un certificat client au serveur pour prouver son identité. Dans ce cas, Azure AD B2C utilisera le certificat que vous chargez dans le cadre de la configuration du connecteur d'API.  Ce comportement se produit dans le cadre de l’établissement d’une liaison SSL. 

Votre service API peut alors limiter l'accès aux seuls services qui disposent des certificats appropriés. Le certificat client est un certificat numérique PKCS12 (PFX) X.509. Dans les environnements de production, il doit être signé par une autorité de certification.

### <a name="http-basic-authentication"></a>Authentification HTTP de base

L’authentification HTTP de base est définie dans le document [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C envoie une requête HTTP avec les informations d'identification du client (`username` et `password`) dans l'en-tête `Authorization`. Les informations d'identification sont mises en forme en tant que chaîne `username:password` codée en base64. Votre API vérifie ensuite ces valeurs pour déterminer si elle doit rejeter ou non un appel d'API.

::: zone pivot="b2c-custom-policy"

### <a name="bearer-token"></a>Jeton du porteur

L’authentification du jeton du porteur est définie dans le document [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Dans l’authentification par jeton du porteur, Azure AD B2C envoie une requête HTTP avec un jeton dans l’en-tête d’autorisation.

```http
Authorization: Bearer <token>
```

Un jeton de porteur est une chaîne opaque. Il peut s’agir d’un jeton d’accès JWT ou d’une chaîne que l’API REST attend que le client Azure AD B2C envoie dans l’en-tête Authorization. 
 
::: zone-end

## <a name="rest-api-platform"></a>Plateforme de l’API REST

Votre API REST peut être basée sur n’importe quelle plateforme et écrite dans n’importe quel langage de programmation tant qu’elle est sécurisée et qu’elle peut envoyer et recevoir des revendications au format JSON.

La demande adressée à votre service d’API REST provient de serveurs Azure AD B2C. Le service de l’API REST doit être publié sur un point de terminaison HTTPS accessible publiquement. Les appels d’API REST proviennent d’une adresse IP de centre de données Azure.

Concevez votre service d’API REST et ses composants sous-jacents (tels que la base de données et le système de fichiers) pour qu’ils soient hautement disponibles.


## <a name="next-steps"></a>Étapes suivantes



::: zone pivot="b2c-user-flow"

- Découvrez comment [ajouter un connecteur d’API à un flux d’utilisateur](add-api-connector.md)
- Bien commencer avec nos [exemples](code-samples.md#api-connectors).

::: zone-end

::: zone pivot="b2c-custom-policy"

Consultez les articles suivants pour obtenir des exemples d’utilisation d’un profil technique RESTful :

- [Procédure pas à pas : ajouter un connecteur d’API à un flux d’utilisateur d’inscription](add-api-connector.md)
- [Procédure pas à pas : Ajouter des échanges de revendications de l’API REST aux stratégies personnalisées dans Azure Active Directory B2C](custom-policy-rest-api-claims-exchange.md)
- [Sécuriser vos services d’API REST](secure-rest-api.md)
- [Reference : Profil technique RESTful](restful-technical-profile.md)

::: zone-end