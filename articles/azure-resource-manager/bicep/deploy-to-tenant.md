---
title: Utiliser Bicep pour déployer des ressources sur le locataire
description: Décrit comment déployer des ressources au niveau du locataire dans un fichier Bicep.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: af1ca550a6443fa7791d4f5ac496ae2d0626b176
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111371754"
---
# <a name="tenant-deployments-with-bicep-file"></a>Déploiements de locataires avec fichier Bicep

À mesure que votre organisation évolue, vous pouvez être amené à définir et attribuer des [stratégies](../../governance/policy/overview.md) ou des [contrôles d’accès basés sur les rôles Azure (Azure RBAC)](../../role-based-access-control/overview.md) dans votre locataire Azure AD. Avec les modèles au niveau du locataire, vous pouvez appliquer de façon déclarative des stratégies et attribuer des rôles à un niveau global.

## <a name="supported-resources"></a>Ressources prises en charge

Tous les types de ressources ne peuvent pas être déployés au niveau du locataire. Cette section répertorie les types de ressources pris en charge.

Pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC), utilisez :

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Pour les modèles imbriqués qui sont déployés sur des groupes d’administration, des abonnements ou des groupes de ressources, utilisez :

* [deployments](/azure/templates/microsoft.resources/deployments)

Pour créer des groupes d’administration, utilisez :

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Pour créer des abonnements, utilisez :

* [aliases](/azure/templates/microsoft.subscription/aliases)

Pour la gestion des coûts, utilisez :

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Pour la configuration du portail, utilisez :

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

Les définitions de stratégies intégrées sont des ressources au niveau du locataire, mais vous ne pouvez pas déployer de définitions de stratégies personnalisées au niveau du locataire. Pour obtenir un exemple d’attribution de définition de stratégie intégrée à une ressource, consultez [Exemple tenantResourceId](./bicep-functions-resource.md#tenantresourceid).

## <a name="set-scope"></a>Étendue de jeu

Pour définir l’étendue sur locataire, utilisez :

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>Accès requis

Le principal déployant le modèle doit être autorisé à créer des ressources au niveau du locataire. Le principal doit être autorisé à exécuter les actions de déploiement (`Microsoft.Resources/deployments/*`) et à créer les ressources définies dans le modèle. Par exemple, pour créer un groupe d’administration, le principal doit disposer de l’autorisation Contributeur au niveau du locataire. Pour créer des attributions de rôles, le principal doit disposer de l’autorisation Propriétaire.

L’administrateur général d'Azure Active Directory n'est pas automatiquement autorisé à attribuer des rôles. Pour activer les déploiements de modèles au niveau du locataire, l’Administrateur général doit procéder comme suit :

1. Élever l’accès au compte de manière à permettre à l'Administrateur général d'attribuer des rôles. Pour plus d’informations, consultez [Élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Attribuer le rôle Propriétaire ou Contributeur au principal devant déployer les modèles.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Le principal dispose désormais des autorisations requises pour déployer le modèle.

## <a name="deployment-commands"></a>Commandes de déploiement

Les commandes utilisées pour les déploiements de locataires sont différentes de celles utilisées pour les déploiements de groupes de ressources.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour Azure CLI, utilisez [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create) :

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour Azure PowerShell, utilisez [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

Pour plus d’informations sur les commandes et options de déploiement de modèles Resource Manager, consultez :

* [Déployer des ressources à l’aide de modèles ARM et l’interface CLI Azure](deploy-cli.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](deploy-powershell.md)
* [Déployer des modèles Resource Manager à partir de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du locataire, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement. Les déploiements d’[abonnement](deploy-to-subscription.md) et de [groupe d’administration](deploy-to-management-group.md) nécessitent également un emplacement. Pour les déploiements d’un [groupe de ressources](deploy-to-resource-group.md), l’emplacement du groupe de ressources est utilisé pour stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un fichier nommé _main.bicep_ crée un nom de déploiement par défaut de **main**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Par exemple, si vous créez un déploiement de locataire avec le nom **deployment1** dans **centralus**, vous ne pouvez pas créer par la suite un autre déploiement avec le nom **deployment1** mais un emplacement **westus**. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement dans un locataire, vous pouvez déployer des ressources vers :

* le locataire
* des groupes d’administration dans le locataire
* subscriptions
* groupes de ressources

Une [ressource d’extension](scope-extension-resources.md) peut être étendue à une cible différente de la cible de déploiement.

L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Cette section montre comment spécifier des étendues différentes. Vous pouvez combiner ces différentes étendues dans un seul modèle.

### <a name="scope-to-tenant"></a>Étendue au locataire

Les ressources définies au sein du fichier Bicep sont appliquées au locataire.

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>Étendue au groupe d’administration

Pour cibler un groupe d'administration au sein du locataire, ajoutez un module. Utilisez la [fonction managementGroup](bicep-functions-scope.md#managementgroup) pour définir sa propriété `scope`. Indiquez le nom du groupe d’administration.

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>Étendue à l’abonnement

Pour cibler un abonnement au sein du locataire, ajoutez un module. Utilisez la [fonction d’abonnement](bicep-functions-scope.md#subscription) pour définir sa propriété `scope`. Indiquez l’ID d’abonnement.

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Étendue au groupe de ressources

Pour cibler un groupe de ressources au sein du locataire, ajoutez un module. Utilisez la [fonction resourceGroup](bicep-functions-scope.md#resourcegroup) pour définir sa propriété `scope`. Fournissez l’ID d’abonnement et le nom du groupe de ressources.

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>Créer un groupe d’administration

Le modèle suivant crée un groupe d'administration.

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

Même si votre compte n’est pas autorisé à effectuer un déploiement sur le locataire, vous pouvez créer des groupes d’administration en choisissant une autre étendue de déploiement. Pour plus d’informations, consultez [Groupe d’administration](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Affecter le rôle

Le modèle suivant attribue un rôle au niveau du locataire.

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en apprendre plus sur d’autres étendues, consultez :

* [Déploiements de groupes de ressources](deploy-to-resource-group.md)
* [Déploiements d’abonnements](deploy-to-subscription.md)
* [Déploiements de groupes d’administration](deploy-to-management-group.md)
