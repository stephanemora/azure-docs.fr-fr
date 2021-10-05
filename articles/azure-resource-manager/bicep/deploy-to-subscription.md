---
title: Utiliser Bicep pour déployer des ressources sur l’abonnement
description: Décrit comment créer un fichier Bicep qui déploie des ressources dans l’étendue de l’abonnement Azure. Il montre comment créer un groupe de ressources.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: df9938e209e163b19e666a26dec4176e83fc79e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793737"
---
# <a name="subscription-deployments-with-bicep-files"></a>Déploiements d’abonnements avec des fichiers Bicep

Cet article explique comment définir l’étendue avec Bicep lors du déploiement sur un abonnement.

Pour simplifier la gestion des ressources, vous pouvez déployer des ressources au niveau de votre abonnement Azure. Par exemple, vous pouvez déployer des [stratégies](../../governance/policy/overview.md) et un [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) sur votre abonnement, ce qui les applique à l’ensemble de votre abonnement. Vous pouvez également créer des groupes de ressources au sein de l’abonnement et déployer des ressources sur ces groupes de ressources dans l’abonnement.

> [!NOTE]
> Vous pouvez déployer sur 800 différents groupes de ressources dans un déploiement de niveau abonnement.

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les étendues de déploiement et pour obtenir des conseils pratiques, consultez [Déploiement de ressources dans des abonnements, des groupes d’administration et des locataires avec Bicep](/learn/modules/deploy-resources-scopes-bicep/) sur **Microsoft Learn**.

## <a name="supported-resources"></a>Ressources prises en charge

Tous les types de ressources ne peuvent pas être déployés au niveau de l’abonnement. Cette section répertorie les types de ressources pris en charge.

Pour Azure Blueprints, utilisez :

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Pour les stratégies Azure, utilisez :

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC), utilisez :

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Pour les modèles imbriqués déployés sur des groupes de ressources, utilisez :

* [deployments](/azure/templates/microsoft.resources/deployments)

Pour la création de groupes de ressources, utilisez :

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Pour la gestion de votre abonnement, utilisez :

* [Advisor configurations](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [Change Analysis profile](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [balises](/azure/templates/microsoft.resources/tags)

Les autres types pris en charge sont les suivants :

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>Étendue de jeu

Définissez l’étendue vers l’abonnement, utilisez :

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>Commandes de déploiement

Pour déployer sur un abonnement, utilisez les commandes de déploiement au niveau de l’abonnement.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour l’interface de ligne de commande Azure, utilisez [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create). L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour la commande de déploiement PowerShell, utilisez [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) ou son alias `New-AzSubscriptionDeployment`. L’exemple suivant déploie un modèle pour créer un groupe de ressources :

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau de l’abonnement, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement. Les déploiements de [groupes d’administration](deploy-to-management-group.md) et de [locataires](deploy-to-tenant.md) nécessitent également un emplacement. Pour les déploiements de [groupes de ressources](deploy-to-resource-group.md), l’emplacement du groupe de ressources est utilisé pour stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé _main.json_ crée un nom de déploiement par défaut **principal**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Par exemple, si vous créez un déploiement d’abonnement avec le nom **deployment1** dans **centralus**, vous ne pouvez pas créer par la suite un autre déploiement avec le nom **deployment1**, mais à l’emplacement **westus**. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement dans un abonnement, vous pouvez déployer des ressources vers :

* l’abonnement cible de l’opération
* tout abonnement dans le locataire
* des groupes de ressources dans le même abonnement ou dans d’autres abonnements
* le locataire de l’abonnement

Une [ressource d’extension](scope-extension-resources.md) peut être étendue à une cible différente de la cible de déploiement.

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

### <a name="scope-to-subscription"></a>Étendue à l’abonnement

Pour déployer des ressources vers l’abonnement cible, ajoutez ces ressources avec le mot clé `resource`.

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

Pour obtenir des exemples de déploiement sur l’abonnement, consultez [Créer des groupes de ressources](#create-resource-groups) et [Affecter une définition de stratégie](#assign-policy-definition).

Pour déployer des ressources dans un abonnement différent de l’abonnement de l’opération, ajoutez un [module](modules.md). Utilisez la [fonction d’abonnement](bicep-functions-scope.md#subscription) pour définir la propriété `scope`. Définissez la propriété `subscriptionId` sur l’ID de l’abonnement sur lequel vous souhaitez effectuer le déploiement.

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Étendue au groupe de ressources

Pour déployer des ressources dans un groupe de ressources au sein de l’abonnement, ajoutez un module et définissez sa propriété `scope`. Si le groupe de ressources existent déjà, utilisez la [fonction resourceGroup](bicep-functions-scope.md#resourcegroup) pour définir la valeur de l’étendue. Fournissez le nom du groupe de ressources.

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

Si le groupe de ressources est créé dans le même fichier Bicep, utilisez le nom symbolique du groupe de ressources pour définir la valeur de l’étendue. Pour obtenir un exemple de définition de l’étendue sur le nom symbolique, consultez [Créer un groupe de ressources et des ressources](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Étendue au locataire

Pour créer des ressources au niveau du locataire, ajoutez un module. Utilisez la [fonction locataire](bicep-functions-scope.md#tenant) pour définir sa propriété `scope`.

L’utilisateur qui déploie le modèle doit disposer de l’[accès requis pour déployer au niveau du locataire](deploy-to-tenant.md#required-access).

L’exemple suivant inclut un module qui est déployé sur le locataire.

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Au lieu d’utiliser un module, vous pouvez définir l’étendue sur `tenant()` pour certains types de ressources. L’exemple suivant déploie un groupe d’administration au niveau du locataire.

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

Pour plus d’informations, consultez [Groupe d’administration](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Groupes de ressources

### <a name="create-resource-groups"></a>Créer des groupes de ressources

Pour créer un groupe de ressources, définissez une ressource [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) avec un nom et un emplacement pour le groupe de ressources.

L’exemple suivant crée un groupe de ressources vide.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>Créer un groupe de ressources et ses ressources

Pour créer le groupe de ressources et y déployer des ressources, utilisez un module. Le module comprend les ressources à déployer sur le groupe de ressources. Définissez l’étendue du module sur le nom symbolique du groupe de ressources que vous créez. Vous pouvez effectuer le déploiement sur jusqu’à 800 groupes de ressources.

L’exemple suivant crée un groupe de ressources, et déploie un compte de stockage sur le groupe de ressources. Notez que la propriété `scope` du module est définie sur `newRG`, qui est le nom symbolique du groupe de ressources en cours de création.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

Le module utilise un fichier Bicep nommé **storage.bicep** avec le contenu suivant :

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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Affecter une définition de stratégie

L’exemple suivant assigne une définition de stratégie existante à l’abonnement. Si la définition de stratégie utilise des paramètres, fournissez-les en tant qu’objet. Si la définition de stratégie n’accepte pas de paramètres, utilisez l’objet vide par défaut.

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>Créer et attribuer des définitions de stratégie

Vous pouvez [définir](../../governance/policy/concepts/definition-structure.md) et attribuer une définition de stratégie dans le même fichier Bicep.

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>Contrôle d’accès

Pour en savoir plus sur l’attribution de rôles, consultez [Ajouter des attributions de rôle Azure à l’aide de modèles Resource Manager](../../role-based-access-control/role-assignments-template.md).

L’exemple suivant crée un groupe de ressources, lui applique un verrou et attribue un rôle à un principal.

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

L’exemple ci-dessous montre le module pour appliquer le verrou :

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

L’exemple suivant montre le module pour attribuer le rôle :

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre plus sur d’autres étendues, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
* [Déploiements de locataires](deploy-to-tenant.md)
