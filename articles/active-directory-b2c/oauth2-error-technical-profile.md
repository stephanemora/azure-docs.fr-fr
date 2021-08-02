---
title: Définir un profil technique d’erreur personnalisée OAuth2 dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique d’erreur personnalisée OAuth2 dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/26/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6fc4f9e7be394a8c63bb95969a2928c63b0c122d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962331"
---
# <a name="define-an-oauth2-custom-error-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique d’erreur personnalisée OAuth2 dans une stratégie personnalisée Azure Active Directory B2C

Cet article explique comment gérer une erreur personnalisée OAuth2 avec Azure Active Directory B2C (Azure AD B2C). Utilisez ce profil technique si une logique ne fonctionne pas correctement dans votre stratégie. Le profil technique retourne une erreur à votre application OAuth2 ou OpenId Connect basée sur les revendications.

Pour gérer le message d’erreur OAuth2 personnalisé :

1. Définissez un profil technique d’erreur OAuth2.
1. Définissez le code d’erreur et les revendications de message d’erreur.
1. À partir du parcours utilisateur, appelez le profil technique d’erreur OAuth2.

## <a name="oauth2-error"></a>Erreur OAuth2

L’erreur retournée affiche les données suivantes :

- **error** - `access_denied`
- **error_description** : message d’erreur respectant la convention `AAD_Custom_<errorCode>: <errorMessage>`.
- **Correlation ID** : ID de corrélation Azure AD B2C.
- **Timestamp** : horodatage de l’erreur.

L’exemple suivant montre un message d’erreur personnalisé qui a été retourné à l’application https://jwt.ms  :

```http
https://jwt.ms/#error=access_denied&error_description=AAD_Custom_1234%3a+My+custom+error+message%0d%0aCorrelation+ID%3a+233bf9bd-747a-4800-9062-6236f3f69a47%0d%0aTimestamp%3a+2021-03-25+14%3a01%3a23Z%0d%0a
```
  
## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `None`. Définissez l’élément **OutputTokenFormat** sur `OAuth2Error`.

L’exemple suivant montre un profil technique pour `ReturnOAuth2Error` :

```xml
<!--
 <ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ReturnOAuth2Error">
        <DisplayName>Return OAuth2 error</DisplayName>
        <Protocol Name="None" />
        <OutputTokenFormat>OAuth2Error</OutputTokenFormat>
        <CryptographicKeys>
          <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
        </CryptographicKeys>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="errorCode" />
          <InputClaim ClaimTypeReferenceId="errorMessage" />
        </InputClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient une liste de revendications requises pour retourner une erreur OAuth2. 

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- |
| errorCode | Oui | Code d'erreur. | 
| errorMessage | Oui | Message d’erreur. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément CryptographicKeys contient la clé suivante :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| issuer_secret | Oui  | Certificat X509 (jeu de clés RSA). Utilisez la clé `B2C_1A_TokenSigningKeyContainer` que vous avez configurée à l’étape [Bien démarrer avec les stratégies personnalisées](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).|
|

## <a name="invoke-the-technical-profile"></a>Appeler le profil technique

Vous pouvez appeler le profil technique d’erreur OAuth2 à partir d’un parcours ou sous-parcours utilisateur. Définissez le type de l’[étape d’orchestration](userjourneys.md#orchestrationsteps) sur `SendClaims`, en référençant votre profil technique d’erreur OAuth2.

Si le parcours ou sous-parcours utilisateur a déjà une autre étape d’orchestration `SendClaims`, définissez l’attribut `DefaultCpimIssuerTechnicalProfileReferenceId` sur le profil technique de l’émetteur de jeton.

Dans l’exemple suivant :

-  Le parcours utilisateur `SignUpOrSignIn-Custom` définit l’attribut `DefaultCpimIssuerTechnicalProfileReferenceId` sur le profil technique de l’émetteur de jeton `JwtIssuer`. 
- La huitième étape d’orchestration vérifie si `errorCode` existe. Si c’est le cas, appelez le profil technique `ReturnOAuth2Error` pour retourner l’erreur.
- Si `errorCode` n’existe pas, la neuvième étape d’orchestration émet le jeton.

```xml
<UserJourney Id="SignUpOrSignIn-Custom" DefaultCpimIssuerTechnicalProfileReferenceId="JwtIssuer">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="ReturnOAuth2Error">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>errorCode</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
    </OrchestrationStep>

    <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />

  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="next-steps"></a>Étapes suivantes

Découvrir les [Sous-parcours](userjourneys.md)