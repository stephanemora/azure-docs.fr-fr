---
title: Gérer la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: Apprenez-en davantage sur la gestion de la personnalisation de l’authentification unique et des jetons avec les stratégies personnalisées.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43e392979c50d340a10575898edb25b119e1410b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712230"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C : gestion de la personnalisation des configurations SSO et de jetons avec des stratégies personnalisées
L’utilisation de stratégies personnalisées vous fournit le même contrôle sur vos configurations de jetons, de sessions et d’authentification unique (SSO) que les stratégies prédéfinies.  Pour connaître l’effet de chaque paramètre, consultez la documentation [ici](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configuration des revendications et de la durée de vie des jetons
Pour modifier les paramètres de durée de vie de vos jetons, vous devez ajouter un élément `<ClaimsProviders>` dans le fichier de partie de confiance de la stratégie que vous souhaitez affecter.  L’élément `<ClaimsProviders>` est un enfant de `<TrustFrameworkPolicy>`.  À l’intérieur, vous devez indiquer les informations qui modifient la durée de vie de votre jeton.  Le code XML ressemble à cet exemple :

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Durée de vie des jetons d’accès** : la durée de vie des jetons d’accès peut être modifiée en changeant la valeur contenue dans l’élément `<Item>` suivi de Key="token_lifetime_secs" en secondes.  La valeur par défaut dans les stratégies prédéfinies est de 3 600 secondes (60 minutes).

**Durée de vie des jetons d’ID** : la durée de vie des jetons d’ID peut être modifiée en changeant la valeur contenue dans l’élément `<Item>` suivi de Key="id_token_lifetime_secs" en secondes.  La valeur par défaut dans les stratégies prédéfinies est de 3 600 secondes (60 minutes).

**Durée de vie des jetons d’actualisation** : la durée de vie des jetons d’actualisation peut être modifiée en changeant la valeur contenue dans l’élément `<Item>` suivi de Key="refresh_token_lifetime_secs" en secondes.  La valeur par défaut dans les stratégies prédéfinies est de 1 209 600 secondes (14 jours).

**Durée de vie de la fenêtre glissante du jeton d’actualisation** : si vous souhaitez définir une durée de vie pour la fenêtre glissante de votre jeton d’actualisation, modifiez la valeur à l’intérieur de l’élément `<Item>` suivi de Key="rolling_refresh_token_lifetime_secs" en secondes.  La valeur par défaut dans les stratégies prédéfinies est de 7 776 000 secondes (90 jours).  Si vous ne souhaitez pas appliquer une durée de vie pour la fenêtre glissante, remplacez cette ligne par :
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Revendication de l’émetteur (iss)** : si vous souhaitez modifier la revendication de l’émetteur (iss), modifiez la valeur contenue dans l’élément `<Item>` suivi de Key="IssuanceClaimPattern".  Les valeurs possibles sont `AuthorityAndTenantGuid` et `AuthorityWithTfp`.

**Paramétrage de l’ID de stratégie représentant la revendication** : les options permettant de définir cette valeur sont TFP (Trust Framework Policy) et ACR (Authentication Context Reference).  
Nous vous recommandons de choisir l’option TFP. Pour ce faire, vérifiez que l’élément `<Item>` suivi de Key="AuthenticationContextReferenceClaimPattern" existe et que sa valeur est `None`.
Dans votre élément `<OutputClaims>`, ajoutez cet élément :
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Pour l’option ACR, supprimez l’élément `<Item>` suivi de Key="AuthenticationContextReferenceClaimPattern".

**Revendication de sujet (sub)** : cette option est définie par défaut sur ObjectID. Si vous souhaitez que cette option soit définie sur `Not Supported`, procédez comme suit :

Remplacez cette ligne : 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
par cette ligne :
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportement de la session et SSO

Pour changer le comportement des sessions et les configurations de SSO, vous devez ajouter un élément `<UserJourneyBehaviors>` à l’intérieur de l’élément `<RelyingParty>`.  L’élément `<UserJourneyBehaviors>` doit suivre immédiatement l’élément `<DefaultUserJourney>`.  L’intérieur de votre élément `<UserJourneyBehavors>` doit ressembler à ceci :

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configuration de l’authentification unique (SSO)** : pour modifier la configuration de l’authentification unique, vous devez modifier la valeur de l’élément `<SingleSignOn>`.  Les valeurs possibles sont `Tenant`, `Application`, `Policy` et `Disabled`. 

**Durée de vie de la session d’application web (minutes)** : pour modifier la durée de vie de la session d’application web, vous devez modifier la valeur de l’élément `<SessionExpiryInSeconds>`.  La valeur par défaut dans les stratégies prédéfinies est de 86 400 secondes (1 440 minutes).

**Délai d’expiration de la session d’application web** : pour modifier le délai d’expiration de la session d’application web, vous devez modifier la valeur de l’élément `<SessionExpiryType>`.  Les valeurs possibles sont `Absolute` et `Rolling`.
