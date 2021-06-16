---
title: 'Démarrage rapide : Nouvelle attribution de stratégie avec un fichier Bicep (préversion)'
description: Dans ce guide de démarrage rapide, vous allez utiliser un fichier Bicep (préversion) pour créer une attribution de stratégie destinée à identifier les ressources non conformes.
ms.date: 05/20/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 0f48804fd73b073479617aa3359e6d338056fb39
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112007172"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Démarrage rapide : Créer une attribution de stratégie pour identifier des ressources non conformes à l'aide d'un fichier Bicep

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources.
Ce guide de démarrage rapide explique pas à pas comment utiliser un fichier [Bicep (préversion)](https://github.com/Azure/bicep) compilé dans un modèle Azure Resource Manager (modèle ARM) pour créer une attribution de stratégie afin d'identifier les machines virtuelles qui n'utilisent pas de disques managés. À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques managés. Elles sont _non conformes_ à l’attribution de stratégie.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bouton pour déployer le modèle Resource Manager pour l’affectation d’une stratégie Azure à Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.authorization%2Fazurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Version `0.3` ou ultérieure de Bicep installée. Si vous ne disposez pas encore de l'interface CLI Bicep ou si vous devez effectuer une mise à jour, consultez [Installer Bicep (préversion)](../../azure-resource-manager/bicep/install.md).

## <a name="review-the-bicep-file"></a>Examiner le fichier Bicep

Dans ce guide de démarrage rapide, vous allez créer une attribution de stratégie et attribuer une définition de stratégie intégrée appelée _Auditer les machines virtuelles qui n'utilisent pas de disques managés_ (`06a78e20-9358-41c9-923c-fb736d382a4d`). Pour obtenir une liste partielle des stratégies intégrées disponibles, consultez [Exemples Azure Policy](./samples/index.md).

Créez le fichier Bicep suivant sous le nom `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    properties: {
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

La ressource définie dans le fichier est :

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Déployer le modèle

> [!NOTE]
> Le service Azure Policy est gratuit. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Policy](./overview.md).

Une fois l'interface CLI Bicep installée et le fichier créé, vous pouvez déployer le fichier Bicep avec :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Ressources supplémentaires :

- Pour trouver des exemples de modèles supplémentaires, consultez [Modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Pour obtenir des informations de référence sur les modèles, accédez au [document de référence des modèles Azure](/azure/templates/microsoft.authorization/allversions).
- Pour savoir comment développer des modèles Resource Manager, consultez la [documentation Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Pour découvrir le déploiement au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Valider le déploiement

Sélectionnez **Conformité** dans la partie gauche de la page. Recherchez ensuite l’affectation de stratégie _Auditer les machines virtuelles qui n’utilisent pas de disques managés_ que vous avez créée.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Capture d’écran des détails de conformité dans la page Conformité à la stratégie." border="false":::

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous **Ressources non conformes**.

Pour plus d’informations, consultez [Fonctionnement de la conformité](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer l’affectation créée, procédez comme suit :

1. Sélectionnez **Conformité** (ou **Affectations**) dans la partie gauche de la page Azure Policy et recherchez l’affectation de stratégie _Auditer les machines virtuelles qui n’utilisent pas de disques managés_ que vous avez créée.

1. Cliquez avec le bouton droit sur l’affectation de stratégie _Auditer les machines virtuelles qui n’utilisent pas de disques managés_ et sélectionnez **Supprimer l’attribution**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Capture d’écran de l’utilisation du menu contextuel pour supprimer une affectation à partir de la page Conformité." border="false":::

1. Supprimez le fichier `assignment.bicep`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie intégrée à une étendue et vous avez évalué son rapport de conformité. La définition de stratégie permet de vérifier que toutes les ressources dans l’étendue sont conformes, ainsi que d’identifier celles qui ne le sont pas.

Pour en savoir plus sur l’affectation de stratégies visant à vérifier que les nouvelles ressources sont conformes, suivez le tutoriel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)
