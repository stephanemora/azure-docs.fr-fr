---
title: Programmes de résolution de revendication
titleSuffix: Azure AD B2C
description: Découvrez comment utiliser les programme de résolution de revendication dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3370ec8de0fb49b92c0fb4dd429439e293ad1d8b
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949872"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>À propos des résolveurs de revendication dans les stratégies personnalisées d’Azure Active Directory B2C

Les résolveurs de revendication dans les [stratégies personnalisées](active-directory-b2c-overview-custom.md) d’Azure Active Directory B2C (Azure AD B2C) fournissent des informations de contexte sur une demande d’autorisation, telles que le nom de la stratégie, l’ID de corrélation de la demande ou la langue de l’interface utilisateur, entre autres.

Pour utiliser un résolveur de revendication dans une revendication d’entrée ou de sortie, vous définissez une chaîne **ClaimType** sous l’élément [ClaimsSchema](claimsschema.md), puis vous définissez **DefaultValue** sur le résolveur de revendication dans l’élément de la revendication d’entrée ou de sortie. Azure AD B2C lit la valeur du résolveur de revendication, puis l’utilise dans le profil technique.

Dans l’exemple suivant, un type de revendication nommé `correlationId` est défini avec un **DataType** de `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Dans le profil technique, mappez le résolveur de revendication au type de revendication. Azure AD B2C renseigne la valeur du résolveur de revendication `{Context:CorrelationId}` dans la revendication `correlationId` et envoie la demande au profil technique.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Types de résolveur de revendication

Les sections suivantes répertorient les résolveurs de revendication disponibles.

### <a name="culture"></a>Culture

| Revendication | Description | Exemples |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Code ISO à deux lettres pour la langue. | en |
| {Culture:LCID}   | LCID du code de langue | 1033 |
| {Culture:RegionName} | Code ISO à deux lettres pour la région. | US |
| {Culture:RFC5646} | Code de langue RFC5646. | en-US |

### <a name="policy"></a>Stratégie

| Revendication | Description | Exemples |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Nom de la stratégie de partie de confiance. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | ID de locataire de la stratégie de partie de confiance. | votre-locataire.onmicrosoft.com |
| {Policy:TenantObjectId} | ID d’objet de locataire de la stratégie de partie de confiance. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | ID de locataire de l’infrastructure de confiance. | votre-locataire.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Revendication | Description | Exemples |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Paramètre de chaîne de requête `acr_values`. | N/A |
| {OIDC:ClientId} |Paramètre de chaîne de requête `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Paramètre de chaîne de requête `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  Paramètre de chaîne de requête `login_hint`. | someone@contoso.com |
| {OIDC:MaxAge} | Le `max_age`. | N/A |
| {OIDC:Nonce} |Paramètre de chaîne de requête `Nonce`. | defaultNonce |
| {OIDC:Prompt} | Paramètre de chaîne de requête `prompt`. | se connecter |
| {OIDC:Resource} |Paramètre de chaîne de requête `resource`. | N/A |
| {OIDC:scope} |Paramètre de chaîne de requête `scope`. | openid |

### <a name="context"></a>Context

| Revendication | Description | Exemples |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Version de l’infrastructure d’expérience d’identité (numéro de build).  | 1.0.507.0 |
| {Context:CorrelationId} | L’ID de corrélation.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Date et heure UTC.  | 10/10/2018 12:00:00 |
| {Context:DeploymentMode} |Mode de déploiement de la stratégie.  | Production |
| {Context:IPAddress} | Adresse IP utilisateur. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Paramètres non liés au protocole

Tous les noms de paramètre inclus dans le cadre d’une requête OIDC ou OAuth2 peuvent être mappés à une revendication dans le parcours utilisateur. Par exemple, la demande de l’application peut inclure un paramètre de chaîne de requête avec le nom `app_session`, `loyalty_number` ou toute chaîne de requête personnalisée.

| Revendication | Description | Exemples |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Paramètre de chaîne de requête. | hawaii |
| {OAUTH-KV:app_session} | Paramètre de chaîne de requête. | A3C5R |
| {OAUTH-KV:loyalty_number} | Paramètre de chaîne de requête. | 1234 |
| {OAUTH-KV : n’importe quelle chaîne de requête personnalisée} | Paramètre de chaîne de requête. | N/A |

### <a name="oauth2"></a>OAuth2

| Revendication | Description | Exemples |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Jeton d’accès. | N/A |

## <a name="how-to-use-claim-resolvers"></a>Comment utiliser des résolveurs de revendication

### <a name="restful-technical-profile"></a>Profil technique RESTful

Dans un profil technique [RESTful](restful-technical-profile.md), vous pouvez envoyer la langue de l’utilisateur, le nom de la stratégie, l’étendue et l’ID client. Selon ces revendications, l’API REST peut exécuter une logique métier personnalisée et éventuellement déclencher un message d’erreur localisé.

L’exemple suivant montre un profil technique RESTful :

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Connexion directe

À l’aide de résolveurs de revendication, vous pouvez préremplir le nom de connexion ou diriger la connexion vers un fournisseur d’identité sociale spécifique, comme Facebook, LinkedIn ou un compte Microsoft. Pour plus d’informations, consultez [Configurer la connexion directe avec Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personnalisation dynamique de l’interface utilisateur

Azure AD B2C vous permet de transmettre des paramètres de chaîne de requête à vos points de terminaison de définition du contenu HTML afin d’afficher dynamiquement le contenu de la page. Par exemple, vous pouvez changer l’image d’arrière-plan dans la page de connexion ou d’inscription Azure AD B2C en fonction d’un paramètre personnalisé que vous transmettez depuis votre application web ou mobile. Pour plus d’informations, consultez [Configurer dynamiquement l’interface utilisateur à l’aide de stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Vous pouvez également localiser votre page HTML en fonction d’un paramètre de langue ou changer le contenu selon l’ID client.

L’exemple suivant transmet dans la chaîne de requête les paramètres **campaignId** (valeur `hawaii`), **language** (valeur `en-US`), et **app** (représentant l’ID client) :

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Ainsi, Azure AD B2C envoie les paramètres ci-dessus à la page de contenu HTML :

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Profil technique Application Insights

Avec Azure Application Insights et les résolveurs de revendication, vous pouvez obtenir des insights sur le comportement de l’utilisateur. Dans le profil technique Application Insights, vous envoyez des revendications d’entrée qui sont conservées dans Azure Application Insights. Pour plus d’informations, consultez [Suivre le comportement des utilisateurs dans les parcours Azure AD B2C à l’aide d’Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). L’exemple suivant envoie l’ID de stratégie, l’ID de corrélation, la langue et l’ID client à Azure Application Insights.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```
