---
title: Déployer des ressources sur le locataire
description: Décrit comment déployer des ressources au niveau du locataire dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460260"
---
# <a name="create-resources-at-the-tenant-level"></a>Créer des ressources au niveau du locataire

À mesure que votre organisation évolue, vous pouvez être amené à définir et attribuer des [stratégies](../../governance/policy/overview.md) ou des [contrôles d’accès basés sur les rôles](../../role-based-access-control/overview.md) dans votre locataire Azure AD. Avec les modèles au niveau du locataire, vous pouvez appliquer de façon déclarative des stratégies et attribuer des rôles à un niveau global.

## <a name="supported-resources"></a>Ressources prises en charge

Vous pouvez déployer les types de ressources suivants au niveau du locataire :

* [déploiements](/azure/templates/microsoft.resources/deployments) : pour les modèles imbriqués déployés sur des groupes d'administration ou des abonnements.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>schéma

Le schéma que vous utilisez pour les déploiements au niveau du locataire est différent de celui utilisé pour les déploiements de groupes de ressources.

Pour les modèles, utilisez :

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Le schéma d’un fichier de paramètres est le même pour toutes les étendues de déploiement. Fichiers de fichiers de paramètres, utilisez :

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Accès requis

Le principal déployant le modèle doit être autorisé à créer des ressources au niveau du locataire. Le principal doit être autorisé à exécuter les actions de déploiement (`Microsoft.Resources/deployments/*`) et à créer les ressources définies dans le modèle. Par exemple, pour créer un groupe d’administration, le principal doit disposer de l’autorisation Contributeur au niveau du locataire. Pour créer des attributions de rôles, le principal doit disposer de l’autorisation Propriétaire.

L’administrateur général d'Azure Active Directory n'est pas automatiquement autorisé à attribuer des rôles. Pour activer les déploiements de modèles au niveau du locataire, l’Administrateur général doit procéder comme suit :

1. Élever l’accès au compte de manière à permettre à l'Administrateur général d'attribuer des rôles. Pour plus d'informations, consultez [Élever l’accès pour gérer tous les abonnements et groupes d’administration Azure](../../role-based-access-control/elevate-access-global-admin.md).

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

Pour Azure CLI, utilisez [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create) :

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Pour Azure PowerShell, utilisez [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Pour l’API REST, utilisez [Déploiements - Créer ou mettre à jour au niveau du locataire](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du locataire, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé **azuredeploy.json** crée le nom de déploiement par défaut **azuredeploy**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="use-template-functions"></a>Utiliser des fonctions de modèle

Pour les déploiements au niveau du locataire, il existe quelques considérations importantes liées à l’utilisation des fonctions de modèle :

* La fonction [resourceGroup()](template-functions-resource.md#resourcegroup)**n’est pas** prise en charge.
* La fonction [subscription()](template-functions-resource.md#subscription) n’est **pas** prise en charge.
* Les fonctions [reference()](template-functions-resource.md#reference) et [list()](template-functions-resource.md#list) sont prises en charge.
* Utilisez la fonction [tenantResourceId()](template-functions-resource.md#tenantresourceid) pour obtenir l’ID des ressources déployées au niveau du locataire.

  Par exemple, pour obtenir l’ID de ressource d’une définition de stratégie, utilisez :
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  L’ID de ressource retourné possède le format suivant :
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Créer un groupe d’administration

Le [modèle suivant](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) crée un groupe d'administration.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Affecter le rôle

Le [modèle suivant](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) attribue un rôle au niveau du locataire.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution de rôles, voir [Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Vous pouvez également déployer des modèles au [niveau de l’abonnement](deploy-to-subscription.md) ou au [niveau du groupe d'administration](deploy-to-management-group.md).
