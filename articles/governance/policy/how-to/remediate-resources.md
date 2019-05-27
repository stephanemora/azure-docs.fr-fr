---
title: Remédier aux ressources non conformes
description: Cette procédure vous guide tout au long de la correction des ressources qui ne sont pas conformes aux stratégies dans Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: d6753b319bc5bc4cbda18fe486695e5b0266acae
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169657"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Corriger les ressources non conformes avec Azure Policy

Les ressources qui ne sont pas conformes à une stratégie **deployIfNotExists** peuvent être placées dans un état conforme par le biais d’une **correction**. Mise à jour s’effectue en demandant à Azure Policy pour exécuter le **deployIfNotExists** effet de la stratégie affectée sur vos ressources existantes. Cet article explique les étapes nécessaires pour comprendre et exécuter la mise à jour avec Azure Policy.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>Fonctionnement de la sécurité de la correction

Quand Azure Policy exécute le modèle le **deployIfNotExists** définition de stratégie, il utilise un [identité gérée](../../../active-directory/managed-identities-azure-resources/overview.md).
Stratégie Azure crée une identité gérée pour chaque affectation, mais il doit avoir plus d’informations sur les rôles pour accorder l’identité gérée. S’il manque des rôles à l’identité managée, cette erreur est affichée durant l’affectation de la stratégie ou d’une initiative. Lorsque vous utilisez le portail, Azure Policy accorde automatiquement l’identité gérée rôles répertoriés une fois que le démarrage de l’attribution.

![Identité managée : rôle manquant](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Si une ressource modifiée par **deployIfNotExists** est en dehors de l’étendue de l’affectation de stratégie ou que le modèle accède à des propriétés sur des ressources en dehors de l’étendue de l’affectation de stratégie, l’identité managée de l’affectation doit se voir [manuellement accorder l’accès](#manually-configure-the-managed-identity), sinon le déploiement de la correction échoue.

## <a name="configure-policy-definition"></a>Configurer une définition de stratégie

La première étape consiste à définir les rôles dont **deployIfNotExists** a besoin dans la définition de stratégie pour déployer le contenu de votre modèle inclus. Sous la propriété **details**, ajoutez une propriété **roleDefinitionIds**. Il s’agit d’un tableau de chaînes qui correspondent à des rôles dans votre environnement. Pour obtenir un exemple complet, consultez [l’exemple deployIfNotExists](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** utilise l’identificateur de ressource complet et ne prend pas le **roleName** court du rôle. Pour obtenir l’ID du rôle « Contributeur » dans votre environnement, utilisez le code suivant :

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurer manuellement l’identité managée

Lorsque vous créez une affectation à l’aide du portail, Azure Policy génère l’identité gérée et lui accorde les rôles définis dans **roleDefinitionIds**. Dans les conditions suivantes, les étapes permettant de créer l’identité managée et de lui accorder des autorisations doivent être effectuées manuellement :

- Durant l’utilisation du SDK (par exemple, Azure PowerShell)
- Quand une ressource en dehors de l’étendue de l’affectation est modifiée par le modèle
- Quand une ressource en dehors de l’étendue de l’affectation est lue par le modèle

> [!NOTE]
> Azure PowerShell et .NET sont les seuls SDK qui prennent en charge cette fonctionnalité.

### <a name="create-managed-identity-with-powershell"></a>Créer une identité managée avec PowerShell

Pour créer une identité managée pendant l’affectation de la stratégie, vous devez définir **Location** et utiliser **AssignIdentity**. L’exemple suivant obtient la définition de la stratégie intégrée **Déployer Transparent Data Encryption SQL DB**, définit le groupe de ressources cible, puis crée l’affectation.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

La `$assignment` variable contient à présent l’ID du principal de l’identité managée, ainsi que les valeurs standard retournées au moment de la création d’une affectation de stratégie. Elle est accessible via `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Accorder des rôles définis avec PowerShell

La nouvelle identité managée doit effectuer la réplication par le biais d’Azure Active Directory pour pouvoir obtenir les rôles nécessaires. Une fois la réplication terminée, l’exemple suivant parcourt la définition de stratégie dans `$policyDef` à la recherche des ID **roleDefinitionIds** et utilise [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour accorder les rôles à la nouvelle identité managée.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Accorder des rôles définis par le biais du portail

À l’aide du portail, vous pouvez accorder les rôles définis à l’identité managée d’une affectation de deux façons : en utilisant la **gestion des identités et des accès** ou en modifiant l’affectation de la stratégie ou d’une initiative et en cliquant sur **Enregistrer**.

Pour ajouter un rôle à l’identité managée de l’affectation, effectuez les étapes suivantes :

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sélectionnez **Affectations** du côté gauche de la page Azure Policy.

1. Recherchez l’affectation qui a une identité managée, puis cliquez sur le nom.

1. Rechercher la propriété **ID de l’affectation** dans la page de modification. L’ID d’affectation est semblable à ceci :

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Le nom de l’identité managée est la dernière partie de l’ID de ressource d’affectation, c’est-à-dire `2802056bfc094dfb95d4d7a5` dans cet exemple. Copiez cette partie de l’ID de ressource d’affectation.

1. Accédez à la ressource ou au conteneur de ressources parent (groupe de ressources, abonnement, groupe d’administration) auquel la définition de rôle doit être ajoutée manuellement.

1. Cliquez sur le lien **Contrôle d’accès (IAM)** dans la page des ressources, puis cliquez sur **+ Ajouter une attribution de rôle** en haut de la page du contrôle d’accès.

1. Sélectionnez le rôle approprié qui correspond à un **roleDefinitionIds** dans la définition de stratégie.
   Laissez **Attribuer l’accès à** sur la valeur par défaut « Utilisateur, groupe ou application Azure AD ». Dans la zone **Sélectionner**, collez ou tapez la partie de l’ID de ressource d’affectation trouvée plus haut. Une fois la recherche terminée, cliquez sur l’objet portant le même nom pour sélectionner l’ID, puis cliquez sur **Enregistrer**.

## <a name="create-a-remediation-task"></a>Créer une tâche de correction

### <a name="create-a-remediation-task-through-portal"></a>Créer une tâche de mise à jour via le portail

Durant l’évaluation, l’affectation de stratégie avec l’effet **deployIfNotExists** détermine s’il existe des ressources non conformes. Quand des ressources non conformes sont trouvées, les détails sont fournis dans la page **Correction**. Outre la liste des stratégies qui ont des ressources non conformes se trouve l’option permettant de déclencher une **tâche de correction**. Cette option crée un déploiement à partir du modèle **deployIfNotExists**.

Pour créer une **tâche de correction**, effectuez les étapes suivantes :

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

   ![Rechercher Stratégie dans Tous les services](../media/remediate-resources/search-policy.png)

1. Sélectionnez **Correction** sur le côté gauche de la page Azure Policy.

   ![Sélectionnez la mise à jour sur la page de stratégie](../media/remediate-resources/select-remediation.png)

1. Toutes les affectations de stratégie **deployIfNotExists** ayant des ressources non conformes sont incluses sous l’onglet **Stratégies à corriger** et dans la table de données. Cliquez sur une stratégie ayant des ressources non conformes. La page **Nouvelle tâche de correction** s’ouvre.

   > [!NOTE]
   > Pour ouvrir la page **Tâche de correction**, vous pouvez également rechercher la stratégie à partir de la page **Conformité**, cliquer dessus, puis cliquer sur le bouton **Créer une tâche de correction**.

1. Dans la page **Nouvelle tâche de correction**, filtrez les ressources à corriger à l’aide des points de suspension de la section **Étendue** pour sélectionner les ressources enfants à partir de l’endroit où la stratégie est affectée (y compris jusqu’aux objets de ressource individuels). En outre, utilisez la liste déroulante **Emplacements** pour filtrer davantage les ressources. Seules les ressources répertoriées dans la table sont corrigées.

   ![Corriger - sélectionner les ressources à corriger](../media/remediate-resources/select-resources.png)

1. Lancez la tâche de correction une fois les ressources filtrées en cliquant sur **Corriger**. La page de conformité à la stratégie s’ouvre sur l’onglet **Tâches de correction**, qui affiche l’état de la progression des tâches.

   ![Corriger - progression des tâches de mise à jour](../media/remediate-resources/task-progress.png)

1. Cliquez sur la **tâche de correction** dans la page de conformité à la stratégie pour obtenir plus d’informations sur la progression. Le filtrage utilisé pour la tâche est affiché, ainsi qu’une liste des ressources en cours de correction.

1. Dans la page **Tâche de correction**, cliquez avec le bouton droit sur une ressource pour afficher le déploiement de la tâche de correction ou la ressource. À la fin de la ligne, cliquez sur **Événements associés** pour voir des détails tels qu’un message d’erreur.

   ![Corriger : menu contextuel lié aux tâches réalisables pour une ressource](../media/remediate-resources/resource-task-context-menu.png)

Les ressources déployées par le biais d’une **tâche de correction** sont ajoutées à l’onglet **Ressources déployées** sur la page de conformité à la stratégie.

### <a name="create-a-remediation-task-through-azure-cli"></a>Créer une tâche de mise à jour via Azure CLI

Pour créer un **tâche de mise à jour** avec Azure CLI, utilisez la `az policy remediation` commandes. Remplacez `{subscriptionId}` avec votre ID d’abonnement et `{myAssignmentId}` avec votre **deployIfNotExists** ID d’affectation de stratégie.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Pour les autres commandes de mise à jour et des exemples, consultez le [mise à jour de la stratégie az](/cli/azure/policy/remediation) commandes.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Créer une tâche de mise à jour via Azure PowerShell

Pour créer un **tâche de mise à jour** avec Azure PowerShell, utilisez la `Start-AzPolicyRemediation` commandes. Remplacez `{subscriptionId}` avec votre ID d’abonnement et `{myAssignmentId}` avec votre **deployIfNotExists** ID d’affectation de stratégie.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Pour les autres applets de commande de mise à jour et des exemples, consultez le [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) module.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les exemples à l’adresse [exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Comprendre comment [créer par programmation des stratégies](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](getting-compliance-data.md).
- Examinez un groupe d’administration avec [organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).