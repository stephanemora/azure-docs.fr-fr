---
title: Vue d’ensemble des profils techniques dans les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment les profils techniques sont utilisés dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48324d252e22ca898f923e1f0ad9b76df1c10861
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183650"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>À propos des profils techniques dans les stratégies personnalisées d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profil technique fournit une infrastructure avec un mécanisme intégré pour communiquer avec différents types de parties au moyen d’une stratégie personnalisée dans Azure Active Directory B2C (Azure AD B2C). Les profils techniques sont utilisés pour communiquer avec votre locataire Azure AD B2C, créer un utilisateur ou lire un profil utilisateur. Un profil technique peut être déclaré automatiquement pour permettre l’interaction avec l’utilisateur. Par exemple, collecter les informations d’identification de l’utilisateur pour se connecter, puis afficher la page d’inscription ou la page de réinitialisation du mot de passe.

## <a name="type-of-technical-profiles"></a>Types de profils techniques

Un profil technique permet les types de scénarios suivants :

- [Azure Active Directory](active-directory-technical-profile.md) : fournit une assistance pour la gestion des utilisateurs Azure Active Directory B2C.
- [Émetteur de jeton JWT](jwt-issuer-technical-profile.md) : émet un jeton JWT qui est retourné à l’application par partie de confiance.
- **Fournisseur de facteur de téléphone** : authentification multifacteur.
- [OAuth1](oauth1-technical-profile.md) : fédération avec n’importe quel fournisseur d’identité du protocole OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) : fédération avec n’importe quel fournisseur d’identité du protocole OAuth 2.0.
- [OpenID Connect](openid-connect-technical-profile.md) : fédération avec n'importe quel fournisseur d'identité du protocole OpenID Connect.
- [Transformation de revendications](claims-transformation-technical-profile.md) : appelle les transformations de revendications de sortie pour manipuler les valeurs de revendications, valider des revendications ou définir des valeurs par défaut pour un ensemble de revendications de sortie.
- [Fournisseur RESTful](restful-technical-profile.md) : appel aux services d'API REST, comme la validation de l'entrée utilisateur, l'enrichissement des données utilisateur ou l'intégration avec les applications métier.
- [SAML2](saml-technical-profile.md) : fédération avec n’importe quel fournisseur d’identité du protocole SAML.
- [Autodéclaré](self-asserted-technical-profile.md) : interaction avec l’utilisateur. Par exemple, collecter les informations d’identification de l’utilisateur pour se connecter, afficher la page d’inscription ou la réinitialisation du mot de passe.
- [Gestion des sessions](custom-policy-reference-sso.md) : gère différents types de sessions.
- [Application Insights](../azure-monitor/app/usage-overview.md)
- [Mot de passe à usage unique](one-time-password-technical-profile.md) : prend en charge la gestion de la génération et de la vérification d’un mot de passe à usage unique.

## <a name="technical-profile-flow"></a>Flux du profil technique

Tous les types de profils techniques partagent le même concept. Vous pouvez envoyer des revendications d’entrée, exécuter la transformation des revendications et communiquer avec le tiers configuré, comme un fournisseur d’identité, une API REST ou les services d’annuaire Azure AD. Au terme du processus, le profil technique retourne les revendications de sortie et peut exécuter la transformation des revendications de sortie. Le schéma suivant explique comment sont traités les transformations et les mappages référencés dans le profil technique. Quel que soit le tiers avec qui le profil technique interagit, une fois la transformation des revendications exécutée, les revendications de sortie du profil technique sont immédiatement stockées dans le conteneur de revendications.

![Schéma illustrant le workflow du profil technique](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Gestion de session d’authentification unique (SSO)**  : restaure l’état de session du profil technique à l’aide de la [gestion de session d’authentification unique](custom-policy-reference-sso.md).
1. **Transformation des revendications d’entrée** : les revendications d’entrée de chaque [transformation de revendications](claimstransformations.md) d’entrée sont récupérées auprès du conteneur de revendications.  Les revendications de sortie d’une transformation des revendications d’entrée peuvent être des revendications d’entrée d’une transformation de revendications d’entrée ultérieure.
1. **Revendications d’entrée** : les revendications sont récupérées auprès du conteneur de revendications et sont utilisées pour le profil technique. Par exemple, un [profil technique autodéclaré](self-asserted-technical-profile.md) utilise les revendications d’entrée pour préremplir les revendications de sortie fournies par l’utilisateur. Un profil technique d’API REST utilise les revendications d’entrée pour envoyer les paramètres d’entrée au point de terminaison de l’API REST. Azure Active Directory utilise la revendication d’entrée comme identificateur unique pour lire, mettre à jour ou supprimer un compte.
1. **Exécution du profil technique** : le profil technique échange les revendications avec le tiers configuré. Par exemple :
    - Redirigez l’utilisateur vers le fournisseur d’identité pour finaliser la connexion. Une fois connecté, l’utilisateur revient et l’exécution du profil technique se poursuit.
    - Appelez une API REST tout en envoyant les paramètres en tant que InputClaims et récupérant les informations en tant que OutputClaims.
    - Créez ou mettez à jour le compte d’utilisateur.
    - Envoie et vérifie le message texte MFA.
1. **Profils techniques de validation** : un [profil technique autodéclaré](self-asserted-technical-profile.md) peut appeler des [profils techniques de validation](validation-technical-profile.md). Le profil technique de validation valide les données profilées par l’utilisateur et renvoie un message d’erreur ou OK, avec ou sans revendications de sortie. Par exemple, avant qu’Azure AD B2C ne crée un nouveau compte, il vérifie si l’utilisateur existe déjà dans les services d’annuaire. Vous pouvez appeler un profil technique d’API REST pour ajouter votre propre logique métier.<p>La portée des revendications de sortie d’un profil technique de validation se limite au profil technique qui appelle le profil technique de validation, ainsi qu’aux autres profils techniques de validation sous le même profil technique. Si vous souhaitez utiliser les revendications de sortie à l’étape d’orchestration suivante, vous devez ajouter les revendications de sortie sur le profil technique qui invoque le profil technique de validation.
1. **Revendications de sortie** : les revendications sont renvoyées au conteneur de revendications. Vous pouvez utiliser ces revendications dans la prochaine étape d’orchestration, ou dans les transformations de revendications de sortie.
1. **Transformations des revendications de sortie** : les revendications d’entrée de chaque [transformation de revendications](claimstransformations.md) de sortie sont récupérées auprès du conteneur de revendications. Les revendications de sortie du profil technique des étapes précédentes peuvent être des revendications d’entrée d’une transformation de revendications de sortie. Après l’exécution, les revendications de sortie sont replacées dans le conteneur de revendications. Les revendications de sortie d’une transformation de revendications d’entrée peuvent également être des revendications d’entrée d’une transformation de revendications de sortie ultérieure.
1. **Gestion de session d’authentification unique (SSO)**  : conserve les données du profil technique dans la session à l’aide de la [gestion de session d’authentification unique](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Éléments inclus dans le profil technique

Un profil technique peut inclure un autre profil technique pour modifier des paramètres ou ajouter de nouvelles fonctionnalités.  L’élément `IncludeTechnicalProfile` est une référence au profil technique de base dont est dérivé un profil technique. Le nombre de niveaux n’est pas limité.

Par exemple, le profil technique **AAD-UserReadUsingAlternativeSecurityId-NoError** inclut le profil **AAD-UserReadUsingAlternativeSecurityId**. Ce profil technique définit l’élément de métadonnées `RaiseErrorIfClaimsPrincipalDoesNotExist` sur `true`, et génère une erreur si aucun compte de réseau social ne figure dans l’annuaire. **AAD-UserReadUsingAlternativeSecurityId-NoError** remplace ce comportement et désactive ce message d’erreur.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclut le profil technique `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Ni **AAD-UserReadUsingAlternativeSecurityId-NoError** ni **AAD-UserReadUsingAlternativeSecurityId** ne spécifie l’élément **Protocole** requis, car celui-ci est spécifié dans le profil technique **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
