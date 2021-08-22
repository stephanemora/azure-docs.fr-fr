---
title: Utiliser Bicep pour déployer des ressources sur des groupes de ressources
description: Décrit comment déployer des ressources dans un fichier Bicep. Montre comment cibler plusieurs groupes de ressources.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 4d68d0d28763c21574a3fd4f2f4c57561759e51e
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634475"
---
# <a name="resource-group-deployments-with-bicep-files"></a>Déploiements de groupes de ressources avec des fichiers Bicep

Cet article explique comment définir l’étendue avec Bicep lors du déploiement sur un groupe de ressources.

## <a name="supported-resources"></a>Ressources prises en charge

La plupart des ressources peuvent être déployées dans un groupe de ressources. Pour obtenir la liste des ressources disponibles, consultez [Référence de modèle Resource Manager](/azure/templates).

## <a name="set-scope"></a>Étendue de jeu

Par défaut, un fichier Bicep est défini sur l’étendue du groupe de ressources. Si vous souhaitez définir explicitement l’étendue, utilisez :

```bicep
targetScope = 'resourceGroup'
```

Toutefois, il est inutile de définir l’étendue cible sur le groupe de ressources, car cette étendue est utilisée par défaut.

## <a name="deployment-commands"></a>Commandes de déploiement

Pour un déploiement dans un groupe de ressources, utilisez les commandes de déploiement de groupe de ressources.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create). L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-file main.bicep \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour la commande de déploiement PowerShell, utilisez [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile main.bicep `
  -storageAccountType Standard_GRS `
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement dans un groupe de ressources, vous pouvez déployer des ressources vers :

* le groupe de ressources cible pour l’opération de déploiement
* d’autres groupes de ressources dans le même abonnement ou dans d’autres abonnements
* tout abonnement dans le locataire
* le locataire pour le groupe de ressources

Une [ressource d’extension](scope-extension-resources.md) peut être étendue à une cible différente de la cible de déploiement.

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Cette section montre comment spécifier des étendues différentes. Vous pouvez combiner ces différentes étendues dans un seul modèle.

### <a name="scope-to-target-resource-group"></a>Étendue au groupe de ressources cible

Pour déployer des ressources dans le groupe de ressources cible, ajoutez ces ressources au fichier Bicep.

```bicep
// resource deployed to target resource group
resource exampleResource 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

Pour obtenir un exemple de modèle, consultez [Déployer dans le groupe de ressources cible](#deploy-to-target-resource-group).

### <a name="scope-to-different-resource-group"></a>Définir l’étendue sur un autre groupe de ressources

Pour déployer des ressources sur un groupe de ressources qui n’est pas le groupe de ressources cible, ajoutez un [module](modules.md). Utilisez la [fonction resourceGroup](bicep-functions-scope.md#resourcegroup) pour définir la propriété `scope` de ce module.

Si le groupe de ressources se trouve dans un autre abonnement, fournissez l’ID d’abonnement et le nom du groupe de ressources. Si le groupe de ressources se trouve dans le même abonnement que le déploiement en cours, fournissez uniquement le nom du groupe de ressources. Si vous ne spécifiez pas d’abonnement dans la [fonction resourceGroup](bicep-functions-scope.md#resourcegroup), l’abonnement actuel est utilisé.

L’exemple qui suit montre un module qui cible un groupe de ressources dans un autre abonnement.

```bicep
param otherResourceGroup string
param otherSubscriptionID string

// module deployed to different subscription and resource group
module exampleModule 'module.bicep' = {
  name: 'otherSubAndRG'
  scope: resourceGroup(otherSubscriptionID, otherResourceGroup)
}
```

L’exemple suivant montre un module qui cible un groupe de ressources dans le même abonnement.

```bicep
param otherResourceGroup string

// module deployed to resource group in the same subscription
module exampleModule 'module.bicep' = {
  name: 'otherRG'
  scope: resourceGroup(otherResourceGroup)
}
```

Pour obtenir un exemple de modèle, consultez [Déployer sur plusieurs groupes de ressources](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Étendue à l’abonnement

Pour déployer des ressources sur un abonnement, ajoutez un module. Utilisez la [fonction d’abonnement](bicep-functions-scope.md#subscription) pour définir sa propriété `scope`.

Pour effectuer un déploiement sur l’abonnement actuel, utilisez la fonction d’abonnement sans paramètre.

```bicep

// module deployed at subscription level
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

Pour effectuer un déploiement vers un autre abonnement, spécifiez cet ID d’abonnement en tant que paramètre dans la fonction d’abonnement.

```bicep
param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(otherSubscriptionID)
}
```

Pour obtenir un exemple de modèle, consultez [Créer un groupe de ressources](#create-resource-group).

### <a name="scope-to-tenant"></a>Étendue au locataire

Pour créer des ressources au niveau du locataire, ajoutez un module. Utilisez la [fonction locataire](bicep-functions-scope.md#tenant) pour définir sa propriété `scope`.

L’utilisateur qui déploie le modèle doit disposer de l’[accès requis pour déployer au niveau du locataire](deploy-to-tenant.md#required-access).

L’exemple suivant inclut un module qui est déployé sur le locataire.

```bicep
// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Au lieu d’utiliser un module, vous pouvez définir l’étendue sur `tenant()` pour certains types de ressources. L’exemple suivant déploie un groupe d’administration au niveau du locataire.

```bicep
param mgName string = 'mg-${uniqueString(newGuid())}'

// ManagementGroup deployed at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

Pour plus d’informations, consultez [Groupe d’administration](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Déployer dans le groupe de ressources cible

Pour déployer des ressources dans le groupe de ressources cible, définissez ces ressources dans la section `resources` du modèle. Le modèle suivant crée un compte de stockage dans le groupe de ressources qui est spécifié dans l’opération de déploiement.

:::code language="bicep" source="~/azure-docs-bicep-samples/get-started-with-bicep-files/add-output/azuredeploy.bicep":::

## <a name="deploy-to-multiple-resource-groups"></a>Déployer dans plusieurs groupes de ressources

Vous pouvez déployer plusieurs groupes de ressources dans un même fichier Bicep.

> [!NOTE]
> Vous pouvez déployer sur **800 groupes de ressources** en un seul déploiement. En règle générale, cette limitation signifie que vous pouvez déployer sur 1 groupe de ressources spécifié pour le modèle parent et jusqu’à 799 groupes de ressources dans les déploiements imbriqués ou liés. Toutefois, si votre modèle parent contient uniquement des modèles imbriqués ou liés et ne déploie lui-même aucune ressource, vous pouvez inclure jusqu’à 800 groupes de ressources dans les déploiements imbriqués ou liés.

L’exemple suivant déploie deux comptes de stockage. Le premier compte de stockage est déployé sur le groupe de ressources spécifié lors de l’opération de déploiement. Le second compte de stockage est déployé sur le groupe de ressources spécifié dans les paramètres `secondResourceGroup` et `secondSubscriptionID` :

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondStorageLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
var secondStorageName = '${storagePrefix}${uniqueString(secondSubscriptionID, secondResourceGroup)}'

module firstStorageAcct 'storage.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

module secondStorageAcct 'storage.bicep' = {
  name: 'storageModule2'
  scope: resourceGroup(secondSubscriptionID, secondResourceGroup)
  params: {
    storageLocation: secondStorageLocation
    storageName: secondStorageName
  }
}
```

Les deux modules utilisent le même fichier Bicep nommé **storage.bicep**.

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

À partir du déploiement d’un groupe de ressources, vous pouvez basculer sur le niveau d’un abonnement et créer un groupe de ressources. Le modèle suivant déploie un compte de stockage dans le groupe de ressources cible et crée un nouveau groupe de ressources dans l’abonnement spécifié.

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

// resource deployed to target resource group
module firstStorageAcct 'storage2.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

// module deployed to subscription
module newRG 'resourceGroup.bicep' = {
  name: 'newResourceGroup'
  scope: subscription(secondSubscriptionID)
  params: {
    resourceGroupName: secondResourceGroup
    resourceGroupLocation: secondLocation
  }
}
```

L’exemple précédent utilise le fichier Bicep suivant pour le module qui crée le groupe de ressources.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre plus sur d’autres étendues, consultez :

* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
* [Déploiements de locataires](deploy-to-tenant.md)
