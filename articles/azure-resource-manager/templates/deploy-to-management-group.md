---
title: Déployer des ressources dans un groupe d’administration
description: Décrit comment déployer des ressources au niveau du groupe d’administration dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: e3661225dd69721ab223da0b44d69a592abb59bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474307"
---
# <a name="create-resources-at-the-management-group-level"></a>Créer des ressources au niveau du groupe d’administration

En règle générale, vous déployez des ressources Azure sur un groupe de ressources dans votre abonnement Azure. Toutefois, vous pouvez également créer des ressources au niveau du groupe d'administration. Vous utilisez les déploiements au niveau du groupe d'administration pour effectuer des actions qui ont un sens à ce niveau, notamment l’affectation d’un [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) ou l’application de [stratégies](../../governance/policy/overview.md).

Actuellement, pour déployer des modèles au niveau du groupe d’administration, vous devez utiliser l’API REST.

## <a name="supported-resources"></a>Ressources prises en charge

Vous pouvez déployer les types de ressources suivants au niveau de groupe d'administration :

* [deployments](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>schéma

Le schéma que vous utilisez pour les déploiements au niveau du groupe d'administration est différent de celui utilisé pour les déploiements de groupes de ressources.

Pour les modèles, utilisez :

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Fichiers de fichiers de paramètres, utilisez :

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Commandes de déploiement

La commande que vous utilisez pour les déploiements de groupes d'administration est différente de celle utilisée pour les déploiements de groupes de ressources.

Pour l’API REST, utilisez [Déploiements - Créer au niveau du groupe d’administration](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du groupe d'administration, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé **azuredeploy.json** crée le nom de déploiement par défaut **azuredeploy**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="use-template-functions"></a>Utiliser des fonctions de modèle

Pour les déploiements au niveau du groupe d'administration, il existe quelques considérations importantes liées à l’utilisation des fonctions de modèle :

* La fonction [resourceGroup()](template-functions-resource.md#resourcegroup)**n’est pas** prise en charge.
* La fonction [subscription()](template-functions-resource.md#subscription) n’est **pas** prise en charge.
* La fonction [resourceId()](template-functions-resource.md#resourceid) est prise en charge. Elle permet d’obtenir l’ID des ressources qui sont utilisées dans les déploiements au niveau du groupe d'administration. Par exemple, récupérez l’ID de ressource d’une définition de stratégie avec `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Elle retourne l'ID de la ressource au format `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`.
* Les fonctions [reference()](template-functions-resource.md#reference) et [list()](template-functions-resource.md#list) sont prises en charge.

## <a name="create-policies"></a>Création des stratégies

### <a name="define-policy"></a>Définir une stratégie

L'exemple suivant montre comment [définir](../../governance/policy/concepts/definition-structure.md) une stratégie au niveau du groupe d'administration.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        }
    ]
}
```

### <a name="assign-policy"></a>Attribuer la stratégie

L’exemple suivant assigne une définition de stratégie existante au groupe d'administration. Si la stratégie utilise des paramètres, fournissez-les en tant qu’objet. Si la stratégie n’utilise pas de paramètres, utilisez l’objet vide par défaut.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```



## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution de rôles, voir [Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Azure Security Center, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](template-syntax.md). 
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](template-functions.md).