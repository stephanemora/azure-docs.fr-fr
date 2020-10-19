---
title: SubJourneys dans Azure Active Directory B2C | Microsoft Docs
description: Spécifiez l’élément SubJourneys d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952416"
---
# <a name="subjourneys"></a>SubJourneys

Les sous-parcours peuvent être utilisés pour organiser et simplifier le déroulement des étapes d’orchestration dans un parcours utilisateur. Les [parcours utilisateur](userjourneys.md) spécifient des chemins explicites par le biais desquels une stratégie autorise une application par partie de confiance à obtenir les revendications souhaitées pour un utilisateur. L’utilisateur est guidé par ces chemins pour récupérer les revendications qui doivent être présentés à la partie de confiance. Autrement dit, les parcours utilisateur définissent la logique métier de ce par quoi passe un utilisateur final pendant que l’Infrastructure d’expérience d’identité Azure AD B2C traite la requête. Un parcours utilisateur est représenté en tant que séquence d’orchestration qui doit être suivie pour que la transaction réussisse. L’élément [ClaimsExchange](userjourneys.md#claimsexchanges) d’une étape d’orchestration est lié à un seul [profil technique](technical-profiles-overview.md) qui s’exécute.

Un sous-parcours est un regroupement d’étapes d’orchestration qui peuvent être appelées à tout moment dans un parcours utilisateur. Vous pouvez utiliser des sous-parcours pour créer des séquences d’étapes réutilisables ou implémenter la création de branche pour mieux représenter la logique métier.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Section d’un parcours utilisateur

Les sous-parcours se comportent comme des [parcours utilisateur](userjourneys.md), car ils sont représentés comme une séquence d’orchestration qui doit être suivie pour une transaction réussie. Les parcours utilisateur peuvent être appelés eux-mêmes et nécessiter l’exécution d’une étape SendClaims. Les sous-parcours sont des composants de parcours utilisateur qui ne peuvent pas être appelés de façon indépendante, et sont toujours appelés à partir d’un parcours utilisateur.

Le composant clé de la ramification consiste à permettre un meilleur traitement de logique métier dans un parcours utilisateur. Les étapes courantes d’orchestration sont regroupées en éléments individuels à appeler séparément. Un sous-parcours peut simplifier un parcours dans lequel plusieurs étapes d’orchestration sont couplées ensemble (avec les mêmes conditions préalables). Un sous-parcours est appelé uniquement à partir d’un parcours utilisateur, il ne doit pas appeler un autre sous-parcours.

Il existe deux types de sous-parcours :

- **Appel** : retourne le contrôle à l’appelant. Le sous-parcours s’exécute, puis le contrôle est renvoyé à l’étape d’orchestration en cours d’exécution dans le parcours utilisateur.
- **Transfert** : transfère le contrôle au sous-parcours (section irréversible). Le sous-parcours doit avoir une étape SendClaims pour renvoyer les revendications à l’application par partie de confiance.

## <a name="example-scenarios"></a>Exemples de scénarios

### <a name="call-subjourney"></a>Appel SubJourney

Cet appel est utile dans les scénarios suivants :

- Vérification de l’âge : Pour une vérification de l’âge, il existe de nombreux composants partagés parmi les parcours utilisateur. La section permet de compiler les éléments communs en composants partageables.  
- Consentement parental : La création de branche permet de faciliter la conception du consentement parental en nous permettant d’accéder aux revendications de l’utilisateur au cours de l’exécution mineure, en même temps que la possibilité de brancher un parcours utilisateur de consentement après avoir trouvé l’utilisateur doit donner son consentement. 
- De l’inscription à la connexion : Imaginez un scénario dans lequel un utilisateur existe déjà dans le répertoire, mais il a peut-être oublié qu’il avait en fait créé un compte. Il peut être souhaitable, dans ce cas, qu’au lieu de demander à l’utilisateur que les informations d’identification qu’il a entrées existent déjà et de forcer l’utilisateur à redémarrer le trajet, la stratégie soit en mesure d’effectuer un basculement d’un acheminement d’inscription vers un Flow de connexion pour cet utilisateur.  

### <a name="transfer-subjourney"></a>Transfert de sous-parcours

Ce transfert est utile dans les scénarios suivants :

- Présentation d’une page de bloc.
- Test A/B en acheminant la requête vers un sous-parcours pour exécuter et émettre un jeton.

## <a name="adding-a-subjourney-element"></a>Ajout d’un élément SubJourney

L’exemple suivant illustre un élément `SubJourney` de type `Call`, qui renvoie le contrôle au parcours utilisateur.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

L’exemple suivant illustre un élément `SubJourney` de type `Transfer`, qui renvoie le jeton à l’application par partie de confiance.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>Appeler une étape de sous-parcours

Une nouvelle étape d’orchestration de type `InvokeSubJourney` est utilisée pour exécuter un sous-parcours. Voici un exemple qui montre tous les éléments d’exécution de cette étape d’orchestration.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Un sous-parcours ne doit pas appeler un autre sous-parcours.

## <a name="components"></a>Composants

Pour définir les sous-parcours pris en charge par la stratégie, ajoutez un élément **SubJourneys** sous l’élément de niveau supérieur du fichier de stratégie.

L’élément **SubJourneys** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Sous-parcours | 1:n | Un sous-parcours qui définit toutes les constructions nécessaires pour un flux d’utilisateur complet. |

L’élément **SubJourneys** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Id | Oui | Identificateur de sous-parcours qui peut être utilisé par le parcours utilisateur pour référencer le sous-parcours de la stratégie. L’élément **SubJourneyReferenceId** de l’élément [Candidate](userjourneys.md#journeylist) pointe vers cet attribut. |
| Type | Oui | Valeurs possibles : `Call` ou `Transfer`. Pour plus d’informations, consultez [Branche de parcours utilisateur](#user-journey-branching)|

L’élément **SubJourney** contient l’élément suivant :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Séquence d’orchestration qui doit être suivie pour que la transaction réussisse. Chaque parcours utilisateur est composé d’une liste ordonnée d’étapes d’orchestration qui sont exécutées de manière séquentielle. Si une étape échoue, la transaction échoue. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Pour obtenir la liste complète des éléments de l’étape d’orchestration, consultez [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Étapes suivantes

[UserJourneys](userjourneys.md)