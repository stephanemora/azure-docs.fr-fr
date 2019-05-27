---
title: Déterminer les causes de non-conformité
description: Lorsqu’une ressource est non conforme, il existe plusieurs raisons. Découvrez comment trouver ce qui a provoqué la non-conformité.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 6e3e01ca9bd459aa6c6aca8dfaacb98b1267fada
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979342"
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

|Reason | Condition |
|-|-|
|La valeur actuelle doit contenir la valeur cible en tant que clé. |containsKey ou **pas** notContainsKey |
|La valeur actuelle doit contenir la valeur cible. |contient ou **pas** notContains |
|La valeur actuelle doit être égale à la valeur cible. |est égal à ou **pas** notEquals |
|La valeur actuelle doit exister. |Existe |
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

## <a name="compliance-details-for-guest-configuration"></a>Détails de conformité pour la Configuration de l’invité

Pour _audit_ des stratégies dans le _invité Configuration_ catégorie, il peut y avoir plusieurs paramètres évaluées à l’intérieur de la machine virtuelle et vous aurez besoin afficher les détails par paramètre. Par exemple, si vous êtes l’audit pour une liste des applications installées et l’état d’affectation est _Non conforme_, vous devez savoir quelles applications spécifiques sont manquantes.

Vous ne disposez pas accès pour vous connecter directement à la machine virtuelle, mais vous devez créer un rapport sur la raison pour laquelle la machine virtuelle est _Non compatible_. Par exemple, vous pouvez effectuer un audit que les machines virtuelles sont jointes au domaine approprié et incluent l’appartenance au domaine actuel dans les détails de création de rapports.

### <a name="azure-portal"></a>Portail Azure

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur le **vue d’ensemble** ou **conformité** , sélectionnez une affectation de stratégie pour n’importe quel initiative qui contient une définition de stratégie de Configuration de l’invité de _Non compatible_.

1. Sélectionnez un _audit_ stratégie dans l’initiative de _Non compatible_.

   ![Afficher les détails de définition d’audit](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Sur le **conformité des ressources** onglet, les informations suivantes sont fournies :

   - **Nom** -le nom des affectations de Configuration d’invité.
   - **Ressource parente** -la machine virtuelle dans un _Non conforme_ état pour l’attribution sélectionnée de la Configuration de l’invité.
   - **Type de ressource** - le _guestConfigurationAssignments_ nom complet.
   - **Dernière évaluation** - la dernière fois que le service de Configuration de l’invité notifié Azure Policy sur l’état de la machine virtuelle cible.

   ![Afficher les détails de la conformité.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Sélectionnez le nom de l’affectation de Configuration invité dans le **nom** colonne pour ouvrir la **conformité des ressources** page.

1. Sélectionnez le **ressource de la vue** bouton en haut de la page pour ouvrir la **invité attribution** page.

Le **invité attribution** page affiche tous les détails de conformité disponibles. Chaque ligne dans la vue représente une version d’évaluation a été effectuée à l’intérieur de la machine virtuelle. Dans le **raison** colonne, une phrase qui décrit pourquoi l’attribution de l’invité est _Non conforme_ s’affiche. Par exemple, si vous êtes l’audit que les machines virtuelles doivent être joints à un domaine, le **raison** colonne afficherait texte, y compris l’appartenance au domaine actuel.

![Afficher les détails de la conformité.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez également afficher les détails de conformité à partir d’Azure PowerShell. Tout d’abord, assurez-vous que vous avez installé le module de Configuration de l’invité.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Vous pouvez afficher l’état actuel de toutes les attributions d’invité pour une machine virtuelle à l’aide de la commande suivante :

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Pour afficher uniquement les _raison_ une phrase qui décrit la raison pour laquelle la machine virtuelle est _Non conforme_, retourner uniquement la propriété enfant de raison.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Vous pouvez également générer un historique de conformité des affectations d’invité dans l’étendue de la machine virtuelle. La sortie de cette commande inclut les détails de chaque rapport pour la machine virtuelle.

> [!NOTE]
> La sortie peut retourner un gros volume de données. Il est recommandé de stocker la sortie dans une variable.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Pour simplifier cet affichage, utilisez le **ShowChanged** paramètre. La sortie de cette commande inclut uniquement les rapports de suivi d’un changement d’état de conformité.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Historique des modifications (version préliminaire)

Dans le cadre d’un nouveau **version préliminaire publique**, les 14 derniers jours de l’historique des modifications sont disponibles pour toutes les ressources Azure qui prennent en charge [mode suppression](../../../azure-resource-manager/complete-mode-deletion.md). L'historique des modifications indique quand une modification a été détectée et fournit un _différentiel visuel_ pour chaque modification. Une détection des modifications est déclenchée lorsque les propriétés de Resource Manager sont ajoutées, supprimées ou modifiées.

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur le **vue d’ensemble** ou **conformité** , sélectionnez une stratégie dans les **état de conformité**.

1. Sous le **conformité des ressources** onglet de la **conformité à la stratégie** page, sélectionnez une ressource.

1. Sélectionnez l'onglet **Historique des modifications (préversion)** de la page **Conformité des ressources**. La liste des modifications détectées, le cas échéant, s'affiche.

   ![Onglet Historique des modifications de stratégie Azure sur la page de conformité des ressources](../media/determine-non-compliance/change-history-tab.png)

1. Sélectionnez une des modifications détectées. Le _diff visual_ pour la ressource est présentée sur la **l’historique des modifications** page.

   ![Diff Visual Azure stratégie modification historique sur la page de l’historique de modification](../media/determine-non-compliance/change-history-visual-diff.png)

Le _différentiel visuel_ aide à identifier les modifications apportées à une ressource. Les modifications détectées ne peuvent pas être liées à l’état de conformité actuel de la ressource.

Modification des données d’historique sont fournies par [graphique des ressources Azure](../../resource-graph/overview.md). Pour interroger ces informations en dehors du portail Azure, consultez [obtenir les modifications des ressources](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les exemples à l’adresse [exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Comprendre comment [créer par programmation des stratégies](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](getting-compliance-data.md).
- Découvrez comment [corriger les ressources non conformes](remediate-resources.md).
- Examinez un groupe d’administration avec [organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).