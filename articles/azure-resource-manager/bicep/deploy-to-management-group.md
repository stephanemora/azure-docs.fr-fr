---
title: Utilisez Bicep pour déployer des ressources dans un groupe d’administration
description: Décrit comment créer un fichier Bicep qui déploie des ressources dans l’étendue du groupe d’administration.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 58fbed44045a90f4f344117fd76f7de8b0493771
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372191"
---
# <a name="management-group-deployments-with-bicep-files"></a>Déploiements de groupes d’administration avec des fichiers Bicep

Cet article explique comment définir l’étendue avec Bicep lors du déploiement sur un groupe d’administration.

À mesure que votre organisation devient plus mature, vous pouvez déployer un fichier Bicep pour créer des ressources au niveau du groupe d’administration. Par exemple, vous pouvez être amené à définir et attribuer des [stratégies](../../governance/policy/overview.md) ou un [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) pour un groupe d’administration. Avec des modèles au niveau du groupe d’administration, vous pouvez de façon déclarative appliquer des stratégies et assigner des rôles au niveau du groupe d’administration.

## <a name="supported-resources"></a>Ressources prises en charge

Certains types de ressources ne peuvent pas être déployés au niveau du groupe d’administration. Cette section répertorie les types de ressources pris en charge.

Pour Azure Blueprints, utilisez :

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Pour Azure Policy, utilisez :

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC), utilisez :

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Pour les modèles imbriqués qui sont déployés sur des abonnements ou des groupes de ressources, utilisez :

* [deployments](/azure/templates/microsoft.resources/deployments)

Pour gérer vos ressources, utilisez :

* [balises](/azure/templates/microsoft.resources/tags)

Les groupes d’administration sont des ressources de niveau client. Toutefois, vous pouvez créer des groupes d’administration dans un déploiement de groupe d’administration en définissant l’étendue du nouveau groupe d’administration sur le locataire. Consultez [Groupe d’administration](#management-group).

## <a name="set-scope"></a>Étendue de jeu

Pour définir l’étendue sur le groupe d’administration, utilisez :

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>Commandes de déploiement

Pour opérer un déploiement vers un groupe d’administration, utilisez les commandes de déploiement de groupes d’administration.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create) :

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour Azure PowerShell, utilisez [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du groupe d'administration, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement. Les déploiements d’[abonnement](deploy-to-subscription.md) et de [locataire](deploy-to-tenant.md) nécessitent également un emplacement. Pour les déploiements d’un [groupe de ressources](deploy-to-resource-group.md), l’emplacement du groupe de ressources est utilisé pour stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé _main.bicep_ crée le nom de déploiement par défaut **main**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Par exemple, si vous créez un déploiement de groupe d’administration avec le nom **deployment1** dans **centralus**, vous ne pouvez pas créer par la suite un autre déploiement avec le nom **deployment1** mais un emplacement **westus**. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement sur un groupe d’administration, vous pouvez déployer des ressources vers :

* le groupe d’administration cible de l’opération
* un autre groupe d’administration dans le locataire
* des abonnements dans le groupe d’administration
* les groupes de ressources dans le groupe d’administration
* le locataire pour le groupe de ressources

Une [ressource d’extension](scope-extension-resources.md) peut être étendue à une cible différente de la cible de déploiement.

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

### <a name="scope-to-management-group"></a>Étendue au groupe d’administration

Pour déployer des ressources dans le groupe d’administration cible, ajoutez ces ressources avec le mot clé `resource`.

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

Pour cibler un autre groupe d'administration, ajoutez un module. Utilisez la [fonction managementGroup](bicep-functions-scope.md#managementgroup) pour définir la propriété `scope`. Indiquez le nom du groupe d’administration.

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferntMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>Étendue à l’abonnement

Vous pouvez également cibler des abonnements dans un groupe d’administration. L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Pour cibler un abonnement au sein du groupe d'administration, ajoutez un module. Utilisez la [fonction d’abonnement](bicep-functions-scope.md#subscription) pour définir la propriété `scope`. Indiquez l’ID d’abonnement.

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Étendue au groupe de ressources

Vous pouvez également cibler des groupes de ressources au sein du groupe d’administration. L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Pour cibler un groupe de ressources au sein du groupe d’administration, ajoutez un module. Utilisez la [fonction resourceGroup](bicep-functions-scope.md#resourcegroup) pour définir la propriété `scope`.  Fournissez l’ID d’abonnement et le nom du groupe de ressources.

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>Étendue au locataire

Pour créer des ressources au niveau du locataire, ajoutez un module. Utilisez la [fonction locataire](bicep-functions-scope.md#tenant) pour définir sa propriété `scope`. L’utilisateur qui déploie le modèle doit disposer de l’[accès requis pour déployer au niveau du locataire](deploy-to-tenant.md#required-access).

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

Ou vous pouvez définir l’étendue sur `/` pour certains types de ressources, comme les groupes d’administration. La création d’un nouveau groupe d’administration est décrite dans la section suivante.

## <a name="management-group"></a>Groupe d’administration

Pour créer un groupe d’administration dans un déploiement de groupe d’administration, vous devez définir l’étendue sur `/` pour le groupe d’administration.

L’exemple suivant crée un groupe d’administration dans le groupe d’administration racine.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

L’exemple d’après crée un nouveau groupe d’administration dans le groupe d’administration spécifié comme parent.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'
param parentMGName string

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: parentMG.id
      }
    }
  }
}

resource parentMG 'Microsoft.Management/managementGroups@2020-05-01' existing = {
  name: parentMGName
  scope: tenant()
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>Abonnements

Pour utiliser un modèle ARM afin de créer un nouvel abonnement Azure dans un groupe d’administration, consultez :

* [Créer des abonnements Contrat Entreprise Azure par programmation](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Créer des abonnements Azure pour un Contrat client Microsoft par programmation](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Créer des abonnements Azure pour un Contrat Partenaire Microsoft par programmation](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Pour déployer un modèle qui déplace un abonnement Azure existant vers un nouveau groupe d’administration, consultez [Déplacer des abonnements dans un modèle ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Azure Policy

Les définitions de stratégie personnalisée qui sont déployées sur un groupe d’administration sont des extensions de celui-ci. Pour obtenir l’ID d’une définition de stratégie personnalisée, utilisez la fonction [extensionResourceId()](./bicep-functions-resource.md#extensionresourceid). Les définitions de stratégie intégrées sont des ressources de niveau locataire. Pour obtenir l’ID d’une définition de stratégie intégrée, utilisez la fonction [tenantResourceId()](./bicep-functions-resource.md#tenantresourceid).

L’exemple suivant montre comment [définir](../../governance/policy/concepts/definition-structure.md) une stratégie au niveau du groupe d’administration et l’attribuer.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre plus sur d’autres étendues, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de locataires](deploy-to-tenant.md)
