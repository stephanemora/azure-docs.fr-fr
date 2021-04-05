---
title: Suivre le comportement des utilisateurs avec Application Insights
titleSuffix: Azure AD B2C
description: Découvrez comment activer les journaux d’événements dans Application Insights à partir des parcours utilisateur Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645923"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Suivre le comportement des utilisateurs dans Azure AD B2C à l’aide d’Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) vous permet d’envoyer des données d’événement directement à [Application Insights](../azure-monitor/app/app-insights-overview.md) à l’aide de la clé d’instrumentation fournie à Azure AD B2C. Avec un profil technique Application Insights, vous pouvez obtenir des journaux des événements détaillés et personnalisés pour vos parcours utilisateur :

- Obtenir des informations détaillées sur le comportement des utilisateurs
- Résoudre les problèmes de vos propres stratégies en développement ou en production
- Mesurer les performances
- Créer des notifications à partir d’Application Insights

## <a name="overview"></a>Vue d’ensemble

Pour activer les journaux des événements personnalisés, ajoutez un profil technique Application Insights. Dans le profil technique, vous définissez la clé d’instrumentation Application Insights, le nom de l’événement et les revendications à enregistrer. Pour publier un événement, ajoutez le profil technique comme étape d’orchestration dans un [parcours utilisateur](userjourneys.md).

Quand vous utilisez Application Insights, tenez compte des éléments suivants :

- Il y a un court délai, généralement inférieur à cinq minutes, avant que de nouveaux journaux soient disponibles dans Application Insights.
- Azure AD B2C vous permet de choisir les revendications à enregistrer. N’incluez pas de revendications contenant des données personnelles.
- Pour enregistrer une session utilisateur, vous pouvez utiliser un ID de corrélation afin d’unifier les événements.
- Appelez le profil technique Application Insights directement à partir d’un [parcours utilisateur](userjourneys.md) ou d’un [sous-parcours](subjourneys.md). N’utilisez pas un profil technique Application Insights comme [profil technique de validation](validation-technical-profile.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Quand vous utilisez Application Insights avec Azure AD B2C, vous devez uniquement créer une ressource et obtenir la clé d’instrumentation. Pour plus d’informations, consultez [Création d’une ressource Application Insights dans Azure](../azure-monitor/app/create-new-resource.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre abonnement Azure. Sélectionnez le filtre **Annuaire + abonnement** dans le menu supérieur et sélectionnez l’annuaire qui contient votre abonnement Azure. Ce locataire n’est pas votre locataire Azure AD B2C.
1. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Application Insights**.
1. Sélectionnez **Create** (Créer).
1. Sous **Nom**, entrez un nom pour la ressource.
1. Pour **Type d’application**, sélectionnez **Application web ASP.NET**.
1. Pour **Groupe de ressources**, sélectionnez un groupe existant ou entrez un nom pour un nouveau groupe.
1. Sélectionnez **Create** (Créer).
1. Ouvrez la nouvelle ressource Application Insights, développez **Éléments principaux**, puis copiez la clé d’instrumentation.

![Capture d’écran montrant la clé d’instrumentation dans l’onglet Vue d’ensemble d’Application Insights.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Définir des revendications

Une revendication fournit un stockage temporaire de données lors d’une exécution de stratégie Azure AD B2C. Vous déclarez vos revendications dans l’[élément ClaimsSchema](claimsschema.md).

1. Ouvrez le fichier d’extensions de votre stratégie. Le fichier peut se présenter ainsi : `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** .
1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si vous ne le voyez pas, ajoutez-le.
1. Recherchez l’élément **ClaimsSchema**. Si vous ne le voyez pas, ajoutez-le.
1. Ajoutez les revendications suivantes à l’élément **ClaimsSchema** :

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>Ajouter de nouveaux profils techniques

Les profils techniques peuvent être considérés comme des fonctions dans la stratégie personnalisée. Ces fonctions utilisent l’approche d’[inclusion du profil technique](technicalprofiles.md#include-technical-profile) : un profil technique inclut un autre profil technique et modifie les paramètres ou ajoute de nouvelles fonctionnalités. Le tableau ci-après définit les profils techniques qui servent à ouvrir une session et à publier des événements.

| Profil technique | Tâche |
| ----------------- | -----|
| AppInsights-Common | Profil technique commun avec une configuration type. Il inclut la clé d’instrumentation Application Insights, la collection de revendications à enregistrer et le mode développeur. Les autres profils techniques incluent le profil technique commun et ajoutent d’autres revendications comme le nom de l’événement. |
| AppInsights-SignInRequest | Enregistre un événement **SignInRequest** avec un ensemble de revendications quand une demande de connexion a été reçue. |
| AppInsights-UserSignUp | Enregistre un événement **UserSignUp** quand l’utilisateur déclenche l’option d’inscription dans un parcours d’inscription ou de connexion. |
| AppInsights-SignInComplete | Enregistre un événement **SignInComplete** si l’authentification réussit, quand un jeton a été envoyé à l’application par partie de confiance. |

Ouvrez le fichier *TrustFrameworkExtensions.xml* à partir du pack de démarrage. Ajoutez les profils techniques à l’élément **ClaimsProvider** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Modifiez la clé d’instrumentation dans le profil technique `AppInsights-Common` et remplacez-le par le GUID fourni par votre ressource Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Ajouter les profils techniques en tant qu’étapes d’orchestration

Ajoutez de nouvelles étapes d’orchestration faisant référence aux profils techniques.

> [!IMPORTANT]
> Après avoir ajouté les nouvelles étapes d’orchestration, renumérotez les étapes séquentiellement sans sauter d’entiers de 1 à N.

1. Appelez `AppInsights-SignInRequest` comme deuxième étape d’orchestration pour suivre l’état de réception d’une demande d’inscription ou de connexion.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Avant l’étape d’orchestration `SendClaims`, ajoutez une nouvelle étape qui appelle `AppInsights-UserSignup`. Celle-ci est déclenchée quand l’utilisateur sélectionne le bouton d’inscription dans un parcours d’inscription ou de connexion.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
   <OrchestrationStep Order="8" Type="ClaimsExchange">
     <Preconditions>
       <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
         <Value>newUser</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
       <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
         <Value>newUser</Value>
         <Value>false</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
     </Preconditions>
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Après l’étape d’orchestration `SendClaims`, appelez `AppInsights-SignInComplete`. Cette étape montre la réussite d’un parcours.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Charger votre fichier, exécuter la stratégie et afficher les événements

Enregistrez et chargez le fichier *TrustFrameworkExtensions.xml*. Ensuite, appelez la stratégie de partie de confiance à partir de votre application ou utilisez l’option **Exécuter maintenant** dans le portail Azure. Attendez que vos événements soient disponibles dans Application Insights.

1. Ouvrez la ressource **Application Insights** dans votre locataire Azure Active Directory.
1. Sélectionnez **Utilisation**, puis **Événements**.
1. Définissez **Pendant** sur **Dernière heure** et **Par** sur **3 minutes**. Vous devrez peut-être actualiser la fenêtre pour voir les résultats.

![Capture d’écran montrant les statistiques d’événements Application Insights.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Collecter des données supplémentaires

Pour répondre aux besoins de votre entreprise, vous souhaiterez peut-être enregistrer davantage de revendications. Pour ajouter une revendication, commencez par [définir une revendication](#define-claims), puis ajoutez la revendication à la collection de revendications d’entrée. Les revendications que vous ajoutez au profil technique **AppInsights-Common** s’affichent dans tous les événements. Les revendications que vous ajoutez à un profil technique spécifique s’affichent uniquement dans cet événement. L’élément de revendication d’entrée contient les attributs suivants :

- **ClaimTypeReferenceId** est la référence à un type de revendication.
- **PartnerClaimType** est le nom de la propriété qui s’affiche dans Azure Insights. Utilisez la syntaxe `{property:NAME}`, où `NAME` est une propriété qui est ajoutée à l’événement.
- **DefaultValue** est une valeur prédéfinie à enregistrer telle que le nom d’un événement. Si une revendication utilisée dans le parcours utilisateur est vide, la valeur par défaut est utilisée. Par exemple, la revendication `identityProvider` est définie par les profils techniques de fédération, tels que Facebook. Si la revendication est vide, cela indique que l’utilisateur s’est connecté avec un compte local. Par conséquent, la valeur par défaut est définie sur **local**. Vous pouvez également enregistrer un [outil de résolution des revendications](claim-resolver-overview.md) avec une valeur contextuelle telle que l’ID d’application ou l’adresse IP de l’utilisateur.

### <a name="manipulate-claims"></a>Manipuler les revendications

Vous pouvez utiliser des [transformations de revendications d’entrée](custom-policy-trust-frameworks.md#manipulating-your-claims) pour modifier les revendications d’entrée ou en générer de nouvelles avant de les envoyer à Application Insights. Dans l’exemple suivant, le profil technique comprend la transformation des revendications d’entrée `CheckIsAdmin`.

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Ajouter des événements

Pour ajouter un événement, créez un nouveau profil technique qui comprend le profil technique `AppInsights-Common`. Ajoutez ensuite le nouveau profil technique comme étape d’orchestration au [parcours utilisateur](custom-policy-trust-frameworks.md#orchestration-steps). Utilisez l’élément [Precondition](userjourneys.md#preconditions) pour déclencher l’événement quand vous êtes prêt. Par exemple, signalez l’événement uniquement quand des utilisateurs passent par l’authentification multifacteur.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Quand vous ajoutez un événement au parcours utilisateur, n’oubliez pas de renuméroter les étapes d’orchestration de manière séquentielle.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Activer le mode développeur

Quand vous utilisez Application Insights pour définir des événements, vous pouvez indiquer si le mode développeur est activé. Le mode développeur contrôle la façon dont les événements sont mis en mémoire tampon. Dans un environnement de développement avec un volume minimal d’événements, l’activation du mode développeur entraîne l’envoi immédiat des événements à Application Insights. La valeur par défaut est `false`. N’activez pas le mode développeur dans les environnements de production.

Pour activer le mode développeur, affectez la valeur `true` à `DeveloperMode` dans les métadonnées du profil technique `AppInsights-Common` :

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Désactiver la télémétrie

Pour désactiver les journaux Application Insights, affectez la valeur `true` à `DisableTelemetry` dans les métadonnées du profil technique `AppInsights-Common` :

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer des tableaux de bord d’indicateurs de performance clés à l’aide d’Azure Application Insights](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end