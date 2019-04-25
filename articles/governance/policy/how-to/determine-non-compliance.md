---
title: Déterminer les causes de non-conformité
description: Lorsqu’une ressource est non conforme, il existe plusieurs raisons. Découvrez comment trouver ce qui a provoqué la non-conformité.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/30/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 0af3fd8596bf558f9d5cc97c95be773aa40954cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499330"
---
# <a name="determine-causes-of-non-compliance"></a>Déterminer les causes de non-conformité

Quand une ressource Azure est évaluée comme non conformes à une règle de stratégie, il est utile de comprendre quelle partie de la règle de la ressource n’est pas conforme avec. Il est également utile de comprendre quelles modifications modifié une ressource précédemment conforme pour le rendre non conformes. Il existe deux façons de rechercher ces informations :

> [!div class="checklist"]
> - [Détails de la conformité](#compliance-details)
> - [Historique des modifications (version préliminaire)](#change-history-preview)

## <a name="compliance-details"></a>Détails de conformité

Lorsqu’une ressource est non conforme, les détails de conformité pour cette ressource sont disponibles à partir de la **conformité à la stratégie** page. Le volet d’informations de conformité inclut les informations suivantes :

- Détails des ressources telles que nom, type, emplacement et ID de ressource
- État de conformité et l’horodatage de la dernière évaluation pour l’attribution de stratégie actuelle
- Une liste de _raisons_ la ressource cas de non-conformité

> [!IMPORTANT]
> En tant que les détails de conformité pour un _Non conforme_ ressource indique la valeur actuelle de propriétés sur cette ressource, l’utilisateur doit disposer **lire** opération pour le **type** de ressource. Par exemple, si le _Non conforme_ ressource est **Microsoft.Compute/virtualmachines** alors l’utilisateur doit avoir le **Microsoft.Compute/virtualMachines/read** opération. Si l’utilisateur n’a pas l’opération nécessaire, une erreur d’accès s’affiche.

Pour afficher les détails de conformité, procédez comme suit :

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur le **vue d’ensemble** ou **conformité** , sélectionnez une stratégie dans un **état de conformité** c'est-à-dire _Non compatible_.

1. Sous le **conformité des ressources** onglet de la **conformité à la stratégie** page, avec le bouton droit ou sélectionnez les points de suspension d’une ressource dans un **état de conformité** c'est-à-dire  _Non conforme_. Puis sélectionnez **afficher les détails de conformité**.

   ![Option Afficher les détails la conformité](../media/determine-non-compliance/view-compliance-details.png)

1. Le **détails de conformité** volet affiche des informations à partir de la dernière version d’évaluation de la ressource à l’affectation de stratégie actuel. Dans cet exemple, le champ **Microsoft.Sql/servers/version** n’est pas _12.0_ alors que la définition de stratégie attendue _14.0_. Si la ressource est non conforme pour plusieurs raisons, chacun est répertorié dans ce volet.

   ![Volet d’informations de conformité et les raisons de non-conformité](../media/determine-non-compliance/compliance-details-pane.png)

   Pour un **auditIfNotExists** ou **deployIfNotExists** définition de stratégie, les détails incluent le **details.type** propriété et les propriétés facultatives. Pour obtenir la liste, consultez [auditIfNotExists propriétés](../concepts/effects.md#auditifnotexists-properties) et [deployIfNotExists propriétés](../concepts/effects.md#deployifnotexists-properties). **Dernière évaluation ressource** est une ressource connexe à partir de la **détails** section de la définition.

   Exemple partielle **deployIfNotExists** définition :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Volet d’informations de conformité - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Pour protéger les données, lorsqu’une valeur de propriété est un _secret_ affiche la valeur actuelle des astérisques.

Ces détails expliquent pourquoi une ressource est actuellement non conforme, mais ne s’affichent lorsque la modification a été apportée à la ressource qui a entraîné son deviennent non conforme. Pour plus d’informations, consultez [historique (version préliminaire) des modifications](#change-history-preview) ci-dessous.

### <a name="compliance-reasons"></a>Raisons de conformité

La matrice suivante mappe chaque possible _raison_ pour le responsable [condition](../concepts/definition-structure.md#conditions) dans la définition de stratégie :

|Motif | Condition |
|-|-|
|La valeur actuelle doit contenir la valeur cible en tant que clé. |containsKey ou **pas** notContainsKey |
|La valeur actuelle doit contenir la valeur cible. |contient ou **pas** notContains |
|La valeur actuelle doit être égale à la valeur cible. |est égal à ou **pas** notEquals |
|La valeur actuelle doit exister. |exists |
|Valeur actuelle doit être dans la valeur cible. |dans ou **pas** notIn |
|La valeur actuelle doit être identique à la valeur cible. |comme ou **pas** notLike |
|La valeur actuelle doit correspondre à la valeur cible (sensibilité à la casse). |correspond à ou **pas** notMatch |
|La valeur actuelle doit correspondre à la valeur cible (non-sensibilité à la casse). |matchInsensitively ou **pas** notMatchInsensitively |
|La valeur actuelle ne doit pas contenir la valeur cible en tant que clé. |notContainsKey ou **pas** containsKey|
|La valeur actuelle ne doit pas contenir la valeur cible. |notContains ou **pas** contient |
|La valeur actuelle ne doit pas être égale à la valeur cible. |notEquals ou **pas** est égal à |
|La valeur actuelle ne doit pas exister. |**pas** existe  |
|La valeur actuelle ne doit pas être dans la valeur cible. |notIn ou **pas** dans |
|La valeur actuelle ne doit pas être identique à la valeur cible. |notLike ou **pas** comme |
|La valeur actuelle ne doit pas correspondre à la valeur cible (sensibilité à la casse). |notMatch ou **pas** correspond à |
|La valeur actuelle ne doit pas correspondre à la valeur cible (non-sensibilité à la casse). |notMatchInsensitively ou **pas** matchInsensitively |
|Aucune ressource associées ne corresponde les détails de l’effet dans la définition de stratégie. |Une ressource du type défini dans **then.details.type** et associées à la ressource définie dans le **si** partie de la règle de stratégie n’existe pas. |

## <a name="change-history-preview"></a>Historique des changements (préversion)

Dans le cadre d’un nouveau **version préliminaire publique**, au cours des 14 derniers jours de modification de l’historique n’est disponible pour toutes les ressources Azure qui prennent en charge [mode suppression](../../../azure-resource-manager/complete-mode-deletion.md). L'historique des modifications indique quand une modification a été détectée et fournit un _différentiel visuel_ pour chaque modification. Une détection des modifications est déclenchée lorsque les propriétés de Resource Manager sont ajoutées, supprimées ou modifiées.

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur le **vue d’ensemble** ou **conformité** , sélectionnez une stratégie dans les **état de conformité**.

1. Sous le **conformité des ressources** onglet de la **conformité à la stratégie** page, sélectionnez une ressource.

1. Sélectionnez l'onglet **Historique des modifications (préversion)** de la page **Conformité des ressources**. La liste des modifications détectées, le cas échéant, s'affiche.

   ![Onglet Historique des modifications de stratégie sur la page de conformité des ressources](../media/determine-non-compliance/change-history-tab.png)

1. Sélectionnez une des modifications détectées. Le _diff visual_ pour la ressource est présentée sur la **l’historique des modifications** page.

   ![Stratégie modification historique Visual Diff sur la page de l’historique de modification](../media/determine-non-compliance/change-history-visual-diff.png)

Le _différentiel visuel_ aide à identifier les modifications apportées à une ressource. Les modifications détectées ne peuvent pas être liées à l’état de conformité actuel de la ressource.

Modification des données d’historique sont fournies par [graphique des ressources Azure](../../resource-graph/overview.md). Pour interroger ces informations en dehors du portail Azure, consultez [obtenir les modifications des ressources](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples à la page [Exemples Azure Policy](../samples/index.md)
- Consulter la page [Structure de définition Azure Policy](../concepts/definition-structure.md)
- Consulter la page [Compréhension des effets d’Azure Policy](../concepts/effects.md)
- Savoir comment [créer des stratégies par programmation](programmatically-create.md)
- Découvrir comment [obtenir des données de conformité](getting-compliance-data.md)
- Découvrir comment [corriger les ressources non conformes](remediate-resources.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).