---
title: Vue d’ensemble des profils techniques dans les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment les profils techniques sont utilisés dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: af08a24ff28d59bf743f92aa69ffa823dcdcc544
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951035"
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
- [Gestion des sessions](active-directory-b2c-reference-sso-custom.md) : gère différents types de sessions.
- **Application Insights**

## <a name="technical-profile-flow"></a>Flux du profil technique

Tous les types de profils techniques partagent le même concept. Vous pouvez envoyer des revendications d’entrée, exécuter la transformation des revendications et communiquer avec le tiers configuré, comme un fournisseur d’identité, une API REST ou les services d’annuaire Azure AD. Une fois le processus terminé, le profil technique retourne les revendications de sortie et peut exécuter la transformation des revendications de sortie. Le schéma suivant explique comment sont traités les transformations et les mappages référencés dans le profil technique. Quel que soit le tiers avec qui le profil technique interagit, une fois la transformation des revendications exécutée, les revendications de sortie du profil technique sont immédiatement stockées dans le conteneur de revendications.

![Schéma illustrant le workflow du profil technique](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** : les revendications d’entrée de chaque [transformation des revendications](claimstransformations.md) d’entrée sont récupérées dans le conteneur de revendications. Une fois l’exécution terminée, les revendications de sortie sont replacées dans le conteneur de revendications. Les revendications de sortie d’une transformation des revendications d’entrée peuvent être des revendications d’entrée d’une transformation de revendications d’entrée ultérieure.
2. **InputClaims** : les revendications sont récupérées auprès du conteneur de revendications et sont utilisées pour le profil technique. Par exemple, un [profil technique autodéclaré](self-asserted-technical-profile.md) utilise les revendications d’entrée pour préremplir les revendications de sortie fournies par l’utilisateur. Un profil technique d’API REST utilise les revendications d’entrée pour envoyer les paramètres d’entrée au point de terminaison de l’API REST. Azure Active Directory utilise la revendication d’entrée comme identificateur unique pour lire, mettre à jour ou supprimer un compte.
3. **Exécution du profil technique** : le profil technique échange les revendications avec le tiers configuré. Par exemple :
    - Redirigez l’utilisateur vers le fournisseur d’identité pour finaliser la connexion. Une fois connecté, l’utilisateur revient et l’exécution du profil technique se poursuit.
    - Appelez une API REST tout en envoyant les paramètres en tant que InputClaims et récupérant les informations en tant que OutputClaims.
    - Créez ou mettez à jour le compte d’utilisateur.
    - Envoie et vérifie le message texte MFA.
4. **ValidationTechnicalProfiles** : pour un [profil technique autodéclaré](self-asserted-technical-profile.md), vous pouvez appeler une entrée [profil technique de validation](validation-technical-profile.md). Le profil technique de validation valide les données profilées par l’utilisateur et renvoie un message d’erreur ou OK, avec ou sans revendications de sortie. Par exemple, avant qu’Azure AD B2C ne crée un nouveau compte, il vérifie si l’utilisateur existe déjà dans les services d’annuaire. Vous pouvez appeler un profil technique d’API REST pour ajouter votre propre logique métier.<p>La portée des revendications de sortie d’un profil technique de validation se limite au profil technique qui invoque le profil technique de validation et aux autres profils techniques de validation sous le même profil technique. Si vous souhaitez utiliser les revendications de sortie à l’étape d’orchestration suivante, vous devez ajouter les revendications de sortie sur le profil technique qui invoque le profil technique de validation.
5. **OutputClaims** : les revendications sont renvoyées au conteneur de revendications. Vous pouvez utiliser ces revendications dans la prochaine étape d’orchestration, ou dans les transformations de revendications de sortie.
6. **OutputClaimsTransformations** : les revendications d’entrée de chaque [transformation de revendications](claimstransformations.md) de sortie sont récupérées auprès du conteneur de revendications. Les revendications de sortie du profil technique des étapes précédentes peuvent être des revendications d’entrée d’une transformation de revendications de sortie. Après l’exécution, les revendications de sortie sont replacées dans le conteneur de revendications. Les revendications de sortie d’une transformation de revendications d’entrée peuvent également être des revendications d’entrée d’une transformation de revendications de sortie ultérieure.
7. La **gestion de session d’authentification unique** - [gestion de session SSO](active-directory-b2c-reference-sso-custom.md) contrôle l’interaction avec l’utilisateur une fois qu’il a été authentifié. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche, ou si des détails de compte local doivent être entrés à nouveau.

Un profil technique peut hériter d’un autre profil technique pour modifier des paramètres ou ajouter de nouvelles fonctionnalités.  L’élément **IncludeTechnicalProfile** est une référence au profil technique de base dont découle un profil technique.

Par exemple, le profil technique **AAD-UserReadUsingAlternativeSecurityId-NoError** inclut le profil **AAD-UserReadUsingAlternativeSecurityId**. Ce profil technique définit l’élément de métadonnées **RaiseErrorIfClaimsPrincipalDoesNotExist** sur `true`, et génère une erreur si aucun compte de réseau social ne figure dans le répertoire. **AAD-UserReadUsingAlternativeSecurityId-NoError** remplace ce comportement et désactive le message d’erreur si l’utilisateur n’existe pas.

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

**UserReadUsingAlternativeSecurityId-AAD-NoError** et **AAD-UserReadUsingAlternativeSecurityId** ne spécifient pas l’élément **Protocole** requis car il est spécifié dans le profil technique **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Un profil technique peut inclure ou hériter d’un autre profil technique, pouvant lui-même en inclure un autre. Le nombre de niveaux n’est pas limité. En fonction des besoins de l’entreprise, votre parcours utilisateur peut appeler **AAD-UserReadUsingAlternativeSecurityId** qui génère une erreur si un compte social d’utilisateur n’existe pas, ou **AAD-UserReadUsingAlternativeSecurityId-NoError** qui ne génère pas d’erreur.











