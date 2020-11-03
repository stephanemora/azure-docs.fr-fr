---
title: Déployer des ressources sur des groupes de ressources
description: Explique comment définir des ressources dans un modèle Azure Resource Manager. Montre comment cibler plusieurs groupes de ressources.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681269"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Déploiements de groupes de ressources avec des modèles Resource Manager

Cet article explique comment étendre votre déploiement à un groupe de ressources. Vous utilisez un modèle Resource Manager pour le déploiement. L’article montre également comment étendre l’étendue au-delà du groupe de ressources dans l’opération de déploiement.

## <a name="supported-resources"></a>Ressources prises en charge

La plupart des ressources peuvent être déployées dans un groupe de ressources. Pour obtenir la liste des ressources disponibles, consultez [Référence de modèle Resource Manager](/azure/templates).

## <a name="schema"></a>schéma

Pour les modèles, utilisez le schéma suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Fichiers de fichiers de paramètres, utilisez :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Commandes de déploiement

Pour un déploiement dans un groupe de ressources, utilisez les commandes de déploiement de groupe de ressources.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create). L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour la commande de déploiement PowerShell, utilisez [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources avec des modèles ARM et le Portail Azure](deploy-portal.md)
* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des ressources avec des modèles Resource Manager et l’API REST Azure Resource Manager](deploy-rest.md)
* [Utiliser un bouton de déploiement pour déployer des modèles à partir du référentiel GitHub](deploy-to-azure-button.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement dans un groupe de ressources, vous pouvez déployer des ressources vers :

* le groupe de ressources cible de l’opération
* d’autres groupes de ressources dans le même abonnement ou dans d’autres abonnements
* les [ressources d’extension](scope-extension-resources.md) peuvent être appliquées aux ressources

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Cette section montre comment spécifier des étendues différentes. Vous pouvez combiner ces différentes étendues dans un seul modèle.

### <a name="scope-to-target-resource-group"></a>Étendue au groupe de ressources cible

Pour déployer des ressources dans la ressource cible, ajoutez ces ressources à la section ressources du modèle.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Étendue au groupe de ressources dans le même abonnement

Pour déployer des ressources dans un autre groupe de ressources du même abonnement, ajoutez un déploiement imbriqué et incluez la propriété `resourceGroup`. Si vous ne spécifiez pas l’ID d’abonnement ni le groupe de ressources, ce sont l’abonnement et le groupe de ressources du modèle parent qui sont utilisés. Tous les groupes de ressources doivent exister avant l’exécution du déploiement.

Dans l’exemple suivant, le déploiement imbriqué cible un groupe de ressources nommé `demoResourceGroup`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Étendue au groupe de ressources dans un abonnement différent

Pour déployer des ressources dans un groupe de ressources sous un abonnement différent, ajoutez un déploiement imbriqué et incluez les propriétés `subscriptionId` et `resourceGroup`. Dans l’exemple suivant, le déploiement imbriqué cible un groupe de ressources nommé `demoResourceGroup`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Groupes inter-ressources

Vous pouvez déployer plusieurs groupes de ressources dans un seul et même modèle Resource Manager. Pour cibler un groupe de ressources différent de celui du modèle parent, utilisez un modèle [imbriqué ou lié](linked-templates.md). Dans le type de ressource du déploiement, spécifiez des valeurs pour l’ID d’abonnement et le groupe de ressources sur lequel vous souhaitez déployer le modèle imbriqué. Les groupes de ressources peuvent exister dans différents abonnements.

> [!NOTE]
> Vous pouvez déployer sur **800 groupes de ressources** en un seul déploiement. En règle générale, cette limitation signifie que vous pouvez déployer sur 1 groupe de ressources spécifié pour le modèle parent et jusqu’à 799 groupes de ressources dans les déploiements imbriqués ou liés. Toutefois, si votre modèle parent contient uniquement des modèles imbriqués ou liés et ne déploie lui-même aucune ressource, vous pouvez inclure jusqu’à 800 groupes de ressources dans les déploiements imbriqués ou liés.

L’exemple suivant déploie deux comptes de stockage. Le premier compte de stockage est déployé sur le groupe de ressources spécifié lors de l’opération de déploiement. Le second compte de stockage est déployé sur le groupe de ressources spécifié dans les paramètres `secondResourceGroup` et `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Si vous définissez `resourceGroup`sur le nom d’un groupe de ressources qui n’existe pas, le déploiement échoue.

Pour tester le modèle précédent et voir les résultats, utilisez PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Déployez deux comptes de stockage sur deux groupes de ressources dans le **même abonnement** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Déployez deux comptes de stockage sur **deux abonnements** en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Azure Security Center, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
