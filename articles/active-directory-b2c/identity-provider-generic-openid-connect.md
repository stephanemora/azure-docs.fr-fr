---
title: Configurer l’inscription et la connexion avec un compte OpenID Connect
titleSuffix: Azure AD B2C
description: Configurez l’inscription et la connexion avec un fournisseur d’identité OpenID Connect dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 688210352385e95c7253ac82d95154eaf707d216
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066311"
---
# <a name="set-up-sign-up-and-sign-in-with-generic-openid-connect-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec OpenID Connect générique à l’aide d’Azure Active Directory B2C

[OpenID Connect](openid-connect.md) est un protocole d’authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour la connexion sécurisée des utilisateurs. La plupart des fournisseurs d’identité qui utilisent ce protocole sont pris en charge dans Azure AD B2C. 

Cet article explique comment ajouter un fournisseur d’identité OpenID Connect personnalisé dans vos flux d’utilisateur.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="add-the-identity-provider"></a>Ajouter le fournisseur d’identité

::: zone pivot="b2c-user-flow"

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Contoso*.

::: zone-end

::: zone pivot="b2c-custom-policy"

Définissez le fournisseur d’identité OpenId Connect en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Login with Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://your-identity-provider.com/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <!-- <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
          </CryptographicKeys> -->
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

::: zone-end

## <a name="configure-the-identity-provider"></a>Configurer le fournisseur d’identité

Chaque fournisseur d’identité OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour effectuer la connexion. Le document des métadonnées inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Le document de métadonnées OpenID Connect est toujours situé dans un point de terminaison qui se termine par `.well-known/openid-configuration`. Pour le fournisseur d’identité OpenID Connect que vous cherchez à ajouter, entrez l’URL de métadonnées.

::: zone pivot="b2c-user-flow"

Dans l’**URL de métadonnées**, entrez l’URL du document de configuration métadonnées OpenID Connect.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans les métadonnées du profil technique `<Item Key="METADATA">`, entrez l’URL du document de métadonnées OpenID Connect.

::: zone-end

## <a name="client-id-and-secret"></a>ID et secret client

Pour permettre aux utilisateurs de se connecter, le fournisseur d’identité exige des développeurs qu’ils inscrivent une application dans leur service. Cette application a un ID (appelé **ID client**) et une **clé secrète client**. 

Le secret client est facultatif. Toutefois, vous devez fournir une clé secrète client si le [Type de réponse](#response-type) `code` utilise le secret pour échanger le code pour le jeton.

::: zone pivot="b2c-user-flow"

Pour ajouter l’ID client et la clé secrète client, copiez ces valeurs à partir du fournisseur d’identité et entrez-les dans les champs correspondants.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans les métadonnées du profil technique `<Item Key="client_id">`, entrez l’ID client.

### <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Si la clé secrète client est requise, stockez celle que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez le filtre **Répertoire + abonnement** dans la barre d’outils du portail.
3. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
4. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
5. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
6. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
7. Pour **Options**, choisissez `Manual`.
8. Entrez un **nom** pour la clé de stratégie. Par exemple : `ContosoSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
9. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
10. Pour **Utilisation de la clé**, sélectionnez `Signature`.
11. Cliquez sur **Créer**.
12. Dans l’élément XML `CryptographicKeys`, ajoutez l’élément suivant :
    
    ```xml
    <CryptographicKeys>
      <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
    </CryptographicKeys>
    ```

::: zone-end

## <a name="scope"></a>Étendue

L’étendue définit les informations et les autorisations que vous souhaitez collecter à partir de votre fournisseur d’identité, par exemple `openid profile`. Pour recevoir le jeton d’ID du fournisseur d’identité, l’étendue `openid` doit être spécifiée. 

Sans jeton d’ID, les utilisateurs ne peuvent pas se connecter à Azure AD B2C à l’aide du fournisseur d’identité personnalisé. D’autres étendues peuvent être ajoutées, séparées par un espace. Reportez-vous à la documentation du fournisseur d’identité personnalisé pour afficher les autres étendues pouvant être disponibles.

::: zone pivot="b2c-user-flow"

Dans **Scope** (Étendue), entrez les étendues du fournisseur d’identité. Par exemple : `openid profile`.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans les métadonnées du profil technique `<Item Key="scope">`, entrez les étendues du fournisseur d’identité. Par exemple : `openid profile`.

::: zone-end

## <a name="response-type"></a>Type de réponse

Le type de réponse décrit le type d’informations renvoyé dans l’appel initial au `authorization_endpoint` du fournisseur d’identité personnalisé. Les types de réponse suivants peuvent être utilisés :

* `code` : conformément au [flux de code d’autorisation](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), un code est renvoyé vers Azure AD B2C. Azure AD B2C appelle ensuite le `token_endpoint` pour échanger le code pour le jeton.
* `id_token` : un jeton d’ID est renvoyé à Azure AD B2C par le fournisseur d’identité personnalisé.

::: zone pivot="b2c-user-flow"

Dans **Type de réponse**, sélectionnez `code` ou `id_token`, en fonction de vos paramètres de fournisseur d’identité.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans les métadonnées du profil technique `<Item Key="response_types">`, sélectionnez `code` ou `id_token`, en fonction de vos paramètres de fournisseur d’identité.

::: zone-end

## <a name="response-mode"></a>Mode de réponse

Le mode de réponse définit la méthode qui doit être utilisée pour renvoyer les données du fournisseur d’identité personnalisé vers Azure Active Directory B2C. Les modes de réponse suivants peuvent être utilisés :

* Le mode de réponse `form_post` est recommandé pour une sécurité optimale. La réponse est transmise via la méthode HTTP `POST`, avec le code ou le jeton encodé dans le corps au format `application/x-www-form-urlencoded`.
* `query` : le code ou le jeton est retourné en tant que paramètre de requête.

::: zone pivot="b2c-user-flow"

Dans **Mode de réponse**, sélectionnez `form_post` ou `query`, en fonction de vos paramètres de fournisseur d’identité.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans les métadonnées du profil technique `<Item Key="response_mode">`, sélectionnez `form_post` ou `query`, en fonction de vos paramètres de fournisseur d’identité.

::: zone-end

## <a name="domain-hint"></a>Indication du domaine

L’[indication du domaine](direct-signin.md) peut être utilisée pour passer directement à la page de connexion du fournisseur d’identité spécifié. Cela évite à l’utilisateur de sélectionner une option dans la liste des fournisseurs d’identité disponibles. 

Pour autoriser ce type de comportement, entrez une valeur pour l’indication du domaine. Pour passer au fournisseur d’identité personnalisé, ajoutez le paramètre `domain_hint=<domain hint value>` à la fin de votre demande lors de l’appel d’Azure AD B2C pour la connexion.

::: zone pivot="b2c-user-flow"

Dans **Conseil de domaine**, entrez un nom de domaine utilisé dans le conseil de domaine.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans l’élément XML du profil technique `<Domain>contoso.com</Domain>`, entrez un nom de domaine utilisé dans le conseil de domaine. Par exemple : `contoso.com`.

::: zone-end

## <a name="claims-mapping"></a>Mappage des revendications

Une fois que le fournisseur d’identité personnalisé a renvoyé un jeton d’ID à Azure AD B2C, Azure AD B2C doit être en mesure de mapper les revendications du jeton reçu aux revendications qu’Azure AD B2C reconnaît et utilise. Pour chacun des mappages suivants, reportez-vous à la documentation du fournisseur d’identité personnalisé pour comprendre les revendications qui sont renvoyées dans les jetons du fournisseur d’identité :

::: zone pivot="b2c-user-flow"

* **ID utilisateur** : entrez la revendication qui fournit l'*identificateur unique* de l'utilisateur connecté.
* **Nom d'affichage** : entrez la revendication qui fournit le *nom d'affichage* ou le *nom complet* de l'utilisateur.
* **Prénom** : entrez la revendication qui fournit le *prénom* de l'utilisateur.
* **Patronyme** : entrez la revendication qui fournit le *nom* de l'utilisateur.
* **Adresse e-mail** : entrez la revendication qui fournit l'*adresse e-mail* de l'utilisateur.


::: zone-end

::: zone pivot="b2c-custom-policy"

L’élément `OutputClaims` contient une liste de revendications que votre fournisseur d’identité retourne. Mappez le nom de la revendication définie dans votre stratégie au nom défini dans le fournisseur d’identité. Sous l’élément `<OutputClaims>`, configurez l’attribut `PartnerClaimType` avec le nom de revendication correspondant défini par votre fournisseur d’identité. 

|ClaimTypeReferenceId  |PartnerClaimType  |
|---------|---------|
| `issuerUserId`| Entrez la revendication qui fournit l’*identificateur unique* pour l’utilisateur connecté.|
| `displayName`| Entrez la revendication qui fournit le *nom d’affichage* ou le *nom complet* de l’utilisateur. |
| `givenName`| Entrez la revendication qui fournit le *prénom* de l’utilisateur.|
| `surName`| Entrez la revendication qui fournit le *nom* de l’utilisateur.|
| `email`| Entrez la revendication qui fournit l’*adresse e-mail* de l’utilisateur.|
| `identityProvider` | Entrez la revendication qui fournit le nom de l’émetteur de jeton. Par exemple : `iss`. Si le fournisseur d’identité n’inclut pas la revendication de l’émetteur dans le jeton, définissez l'attribut `DefaultValue` avec un identificateur unique de votre fournisseur d’identité. Par exemple : `DefaultValue="contoso.com"`.|


::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-the-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d'identité à un flux d'utilisateur 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Cliquez sur le flux d'utilisateur auquel vous souhaitez ajouter le fournisseur d'identité. 
1. Sous **Fournisseurs d'identité sociale**, sélectionnez le fournisseur d'identité que vous avez ajouté. Par exemple, *Contoso*.
1. Sélectionnez **Enregistrer**.

## <a name="test-your-user-flow"></a>Tester votre flux d’utilisateur

1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d'utilisateur**.
1. Sur la page d'inscription ou de connexion, sélectionnez le fournisseur d'identité auquel vous souhaitez vous connecter. Par exemple, *Contoso*.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Contoso** pour vous connecter avec un compte Google.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le guide de référence [Profil technique OpenId Connect](openid-connect-technical-profile.md).

::: zone-end
