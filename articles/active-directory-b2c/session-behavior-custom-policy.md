---
title: Configurer le comportement de session en utilisant des politiques personnalisées – Azure Active Directory B2C | Microsoft Docs
description: Configurez la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961100"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la modification du mot de passe avec des stratégies personnalisées dans Azure Active Directory B2C

La gestion de [session d’authentification unique (SSO)](session-overview.md) dans Azure Active Directory B2C (Azure AD B2C) permet à un administrateur de contrôler l’interaction avec un utilisateur une fois celui-ci authentifié. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche ou si des détails de compte doivent être entrés à nouveau. Cet article décrit comment configurer les paramètres d’authentification unique pour Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriétés de comportement de session

Vous pouvez utiliser les propriétés suivantes pour gérer les sessions d’application web :

- **Durée de vie de session d’application web (minutes)** : la durée de vie du cookie de session Azure AD B2C stocké dans le navigateur de l’utilisateur après une authentification réussie.
  - Valeur par défaut = 86400 secondes (1440 minutes).
  - Minimum (inclusif) = 900 secondes (15 minutes).
  - Maximum (inclusif) = 86400 secondes (1440 minutes).
- **Délai d’expiration de session d’application web** : le [type d’expiration de session](session-overview.md#session-expiry-type), *Rolling* (Propagé) ou *Absolute* (Absolu). 
- **Configuration de l’authentification unique** : l’[étendue de session](session-overview.md#session-scope) du comportement de l’authentification unique (SSO) sur plusieurs applications et flux d’utilisateurs dans votre client Azure AD B2C. 

## <a name="configure-the-properties"></a>Configurer les propriétés

Pour changer le comportement des sessions et les configurations de SSO, vous devez ajouter un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md).  L’élément **UserJourneyBehaviors** doit suivre immédiatement l’élément **DefaultUserJourney**. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>Configurer le comportement de déconnexion

### <a name="secure-your-logout-redirect"></a>Sécuriser la redirection de déconnexion

Après la déconnexion, l’utilisateur est redirigé vers l’URI spécifié dans le paramètre `post_logout_redirect_uri`, quelles que soient les URL de réponse qui ont été spécifiées pour l’application. Cependant, si un `id_token_hint` valide est transmis et que l’option **Exiger un jeton d’ID dans les demandes de déconnexion** est activée, Azure AD B2C vérifie que la valeur de `post_logout_redirect_uri` correspond à l’un des URI de redirection configurés de l’application avant d’effectuer la redirection. Si aucune URL de réponse correspondante n’a été configurée pour l’application, un message d’erreur s’affiche et l’utilisateur n’est pas redirigé. 

Pour exiger un jeton d’ID dans les demandes de déconnexion, ajoutez un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md). Définissez ensuite **EnforceIdTokenHintOnLogout** dans l’élément **SingleSignOn** sur `true`. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

Pour configurer l’URL de déconnexion de votre application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Authentification**.
1. Dans la zone de texte **URL de déconnexion**, saisissez votre URI de redirection après déconnexion, puis sélectionnez **Enregistrer**.

### <a name="single-sign-out"></a>Déconnexion unique

#### <a name="configure-the-applications"></a>Configurez les applications

Lorsque vous redirigez l’utilisateur vers le point de terminaison de déconnexion Azure AD B2C (pour les protocoles OAuth2 et SAML), Azure AD B2C efface la session de l’utilisateur du navigateur.  Pour autoriser la [déconnexion unique](session-overview.md#single-sign-out), définissez le `LogoutUrl` de l’application à partir du portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Sélectionnez votre client Azure AD B2C en cliquant sur votre compte en haut à droite de la page.
1. Dans le menu de gauche, choisissez **Azure AD B2C**, **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Authentification**.
1. Dans la zone de texte **URL de déconnexion**, saisissez votre URI de redirection après déconnexion, puis sélectionnez **Enregistrer**.

#### <a name="configure-the-token-issuer"></a>Configurez le jeton émetteur 

Pour prendre en charge la déconnexion unique, les profils techniques de l’émetteur de jetons pour JWT et SAML doivent spécifier les éléments suivants :

- Le nom du protocole, tel que `<Protocol Name="OpenIdConnect" />`
- La référence au profil technique de session, tel que `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`.

L’exemple suivant illustre les émetteurs de jetons JWT et SAML avec la fonctionnalité de déconnexion unique :

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur une [session Azure AD B2C](session-overview.md).
